# 一 . 为什么需要Vxlan

1. vlan的数量限制
   4096个vlan远不能满足大规模云计算数据中心的需求

2. 物理网络基础设施的限制
   基于IP子网的区域划分限制了需要二层网络连通性的应用负载的部署

3. TOR交换机MAC表耗尽
    虚拟化以及东西向流量导致更多的MAC表项

4. 多租户场景
    IP地址重叠？
# 二. 什么是Vxlan

1. Vxlan报文
    vxlan(virtual Extensible LAN)虚拟可扩展局域网，是一种overlay的网络技术，使用MAC in UDP的方法进
行封装，共50字节的封装报文头。具体的报文格式如下：
http://img.blog.csdn.net/20131116103842062?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZXpndzE5ODU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center

(1) vxlan header
    共计8个字节，目前使用的是Flags中的一个8bit的标识位和24bit的VNI(Vxlan Network identifier)，
其余部分没有定义，但是在使用的时候必须设置为0x0000。

(2) 外层的UDP报头
     目的端口使用4798，但是可以根据需要进行修改。同事UDP的校验和必须设置成全0。

(3) IP报文头
     目的IP地址可以是单播地址，也可以是多播地址。单播情况下，目的IP地址是Vxlan Tunnel End Point
(VTEP)的IP地址。在多播情况下引入VXLAN管理层，利用VNI和IP多播组的映射来确定VTEPs。？？？
protocol：设置值为0x11，显示说明这是UDP数据包
Source ip: 源vTEP_IP;
Destination ip: 目的VTEP IP。
(4) Ethernet Header
Destination Address：目的VTEP的Mac 地址，即为本地下一跳的地址(通常是网关Mac 地址)；
VLAN: VLAN Type被设置为0x8100， 并可以设置Vlan Id tag(这就是vxlan的vlan 标签)。
Ethertype：设置值为0x8000，指明数据包为IPv4的。
补充：VTEP的作用？    
     用于对VXLAN报文进行封装/解封装，包括ARP请求报文和正常的VXLAN数据报文，在一段封装报文
后通过隧道向另一端VTEP发送封装报文，另一端VTEP接收到封装的报文解封装后根据封装的MAC地址
进行装法。VTEP可由支持VXLAN的硬件设备或软件来实现。

   从封装的结构上来看，VXLAN提供了将二层网络overlay在三层网络上的能力，VXLAN Header中的VNI有
24个bit，数量远远大于4096，并且UDP的封装可以穿越三层网络，比VLAN有更好的扩展性。

2. Vxlan的数据和控制平面
  (1) 数据平面---隧道机制
     已经知道，VTEP为虚拟机的数据包加上了层包头，这些新的报头之有在数据到达目的VTEP后才会被去掉。
中间路径的网络设备只会根据外层包头内的目的地址进行数据转发，对于转发路径上的网络来说，一个Vxlan
数据包跟一个普通IP包相比，出了个头大一点外没有区别。
     由于VXLAN的数据包在整个转发过程中保持了内部数据的完整，因此VXLAN的数据平面是一个基于隧道
的数据平面。

(2) 控制平面----改进的二层协议
     VXLAN不会在虚拟机之间维持一个长连接，所以VXLAN需要一个控制平面来记录对端地址可达情况。控制
平面的表为(VNI，内层MAC，外层vtep_ip)。Vxlan学习地址的时候仍然保存着二层协议的特征，节点之间不会
周期性的交换各自的路由表，对于不认识的MAC地址，VXLAN依靠组播来获取路径信息(如果有SDN Controller，
可以向SDN单播获取)。
    另一方面，VXLAN还有自学习的功能，当VTEP收到一个UDP数据报后，会检查自己是否收到过这个虚拟机的
数据，如果没有，VTEP就会记录源vni/源外层ip/源内层mac对应关系，避免组播学习。


## 3. VxlanARP请求

### (1) vxlan初始化

http://img.blog.csdn.net/20131116103939171?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZXpndzE5ODU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center

 VM1和VM2连接到VXLAN网络(VNI)100，两个VXLAN主机加入IP多播组239.119.1.1

### (2) ARP请求
http://img.blog.csdn.net/20131116104014546?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZXpndzE5ODU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center

   1) VM1以广播的形式发送ARP请求；
   2) VTEP1封装报文。打上VXLAN标识为100，外层IP头DA为IP多播组(239.119.1.1)，SA为IP_VTEP1.
   3) VTEP1在多播组内进行多播；
   4) VTEP2解析接收到多播报文。填写流表(VNI, 内层mac地址，外层Ip地址)，并在本地VXLAN标识为100的范围内
       广播(是VXLAN的用武之地)。
   5) VM2对接收到的ARP请求进行响应；

### (3) ARP应答
http://img.blog.csdn.net/20131116104126453?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZXpndzE5ODU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center
  1) VM2准备ARP响应报文后向VM1发送响应报文

       2)VTEP2接收到VM2的响应报文后把它封装在ip单播报文中（VXLAN标识依然为100），然 后向VM1发送单播

       3)VTEP1接收到单播报文后，学习内层MAC到外层ip地址的映射，解封装并根据被封装内容的目的MAC地址转发给VM1

       4)VM1接收到ARP应答报文，ARP交互结束

### 4  数据传输
    (1)  ARP请求应答之后，VM1知道了VM2的Mac地址，并且要向VM2通信(注意，VM1是以TCP的方法向VM2发送数据的)。
VTEP1 收到VM1发送数据包，用MAC地址从流表中检查VM1与VM2是否属于用一个VNI。两个VM不但位于同一个VNI中
(不在同一个VNI中出网关)，并且VTEP1已经知道了VM2的所有地址信息(MAC和VTEP2_IP)。VTEP1封装新的数据包。然后
交给上联交换机。
   (2) 上联交换机收到服务器发来的UDP包，对比目的IP地址和自己的路由表，然后将数据报转发给相应的端口。
   (3) 目的VTEP收到数据包后检查器VNI，如果UDP报中VNI与VM2的VNI一致，则将数据包解封装后交给VM2进一步处理。至此
一个数据包传输完成。整个Vxlan相关的行为(可能穿越多个网关)对虚拟机来说是透明的，虚拟机不会感受传输的过程。

    虽然VM1与VM2之间启动了TCP来传输数据，但数据包一路上实际是以UDP的形式被转发，两端的VTEP并不会检查数据是否
正确或者顺序是否完整，所有的这些工作都是在VM1和VM2在接收到解封装的TCP包后完成的。也就是说如果说如果被UDP封装
的是TCP连接，那么UDP和TCP将做为两个独立的协议栈各自工作，相互之间没有交互。
    
### 5 Vxlan网关

http://img.blog.csdn.net/20131116104212765?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJlZXpndzE5ODU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center
 如果需要VXLAN网络和非VXLAN网络连接，必须使用VXLAN网关才能把VXLAN网络和外部网络进行桥接和
完成VXLAN ID和VLAN ID之间的映射和路由，和VLAN一样，VXLAN网络之间的通信也需要三层设备的支持，
即VXLAN路由的支持。同样VXLAN网关可由硬件和软件来实现。

 从封装的结构上来看，VXLAN提供了将二层网络overlay在三层网络上的能力，VXLAN Header中的VNI有
24个bit，数量远远大于4096，并且UDP的封装可以穿越三层网络，比VLAN有更好的扩展性。

### 6.部署
(1) 纯VXLAN部署场景
  对于连接到VXLAN内的虚拟机，由于虚拟机的VLAN信息不再作为转发的依据，虚拟机的迁移也就
不再受三层网关的限制，可以实现跨越三层网关的迁移。


# (2) VXLAN与VLAN混合部署

   为了实现VLAN和VXLAN之间互通，VXLAN定义了VXLAN网关。VXLAN网关上同时存在两种类型的端口：VXLAN端口
和普通端口。
   当收到从VXLAN网络到普通网络的数据时，VXLAN网关去掉外层包头，根据内层的原始帧头转发到普通端口上；当有数据
从普通网络进入到VXLAN网络时，VXLAN网关负责打上外层包头，并根据原始VLAN ID对应到一个VNI，同时去掉内层包头
的VLAN ID信息。相应的如果VXLAN网关发现一个VXLAN包的内层帧头上还带有原始的二层VLAN ID，会直接将这个包丢弃。
之所以这样，是VLAN ID是一个本地信息，仅仅在一个地方的二层网络上其作用，VXLAN是隧道机制，并不依赖VLAN ID进行
转发，也无法检查VLAN ID正确与否。因此，VXLAN网关连接传统网络的端口必须配置ACCESS口，不能启用TRUNK口
