# RESTful规范

[一、规范类型](#jump1)

[二、RESTful特征](#jump2)

[ 三、总结](#jump3)


## RESTful

### 什么是RESTful？
* REST，就是一种应用接口的设计风格。RESTful 是 REST 的形容词形式，RESTful API 指的是 REST 风格的接口。一般 REST 与 RESTful 是一个意思，区别就是一个是名词，一个是形容词。

***

<span id="jump1"></span>
### 一、规范类型 

|请求类型|URL|说明|
|:-:|:-:|:-:|
|GET|http://api.github.com/v1/cars|用来获取资源|
|POST|http://api.github.com/v1/cars|用来新建资源|
|PUT|http://api.github.com/v1/cars|用来更新资源|
|PATCH|http://api.github.com/v1/cars|通常是部分更新|
|DELETE|http://api.github.com/v1/cars|用来删除资源|

#### URL请求
`GET http://api.github.com/v1/cars`

`POST http://api.github.com/v1/cars`

`PUT http://api.github.com/v1/cars`

`PATCH http://api.github.com/v1/cars`

`DELETE http://api.github.com/v1/cars`

***

<span id="jump2"></span>
### 二、RESTful特征  
* 1、URL的根路径 
`http://api.github.com/v1`

* 2、需要有api版本信息
`http://api.github.com/v1`

* 3、URL中只使用名词指定资源，不用动词，且推荐使用复数
```
http://api.github.com/v1/cars // 获取某个账户下的车辆列表
http://api.github.com/v1/fences // 获取某个账户下的围栏列表
```

* 4、用HTTP协议里的动词来实现资源的添加，修改，删除等操作。即通过HTTP动词来实现资源的状态扭转

`错误使用： GET http://api.github.com/v1/deleteCar 删除车辆`

* 5、GET应该是安全的，不会改变资源状态

`这个应该很好理解，get的时候就只是获取资源，而不涉及添加、更新、删除资源。`

* 6、使用正确的HTTP Status Code返回状态码
`常用的有404，200，500，400等等。`
    
* 7、过滤信息
如果记录数量很多，服务器不可能都将它们返回给用户。API应该提供参数，过滤返回结果。
下面是一些常见的参数:
```
?limit=10：指定返回记录的数量
?offset=10：指定返回记录的开始位置。
?page=2&per_page=100：指定第几页，以及每页的记录数。
?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
?producy_type=1：指定筛选条件
```

* 8、规范返回的数据
为了保障前后端的数据交互的顺畅，建议规范数据的返回，并采用固定的数据格式封装。
接口返回模板：
```
{
    status:0,
    data:{}||[],
    msg:’’
}
```

***

<span id="jump3"></span>
### 三、总结，看一个标准的RESTful api要做到以下

```
看Url就知道要操作的资源是什么，是操作车辆还是围栏
看Http Method就知道操作动作是什么，是添加（post）还是删除（delete）
看Http Status Code就知道操作结果如何，是成功（200）还是内部错误（500）
```









