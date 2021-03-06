# GraphQL快速入门

## 一、GraphQL是什么?
* 关于GraphQL是什么，网上一搜一大堆。根据官网的解释就是一种用于 API 的查询语言。

* 一看到用于API的查询语言，我也是一脸懵逼的。博主你在开玩笑吧？你的翻译水平不过关？API还能查吗？API不是后端写好，前端调用的吗？

* 的确可以，这就是GraphQL强大的地方。
* 引用官方文档的一句话：

> `ask exactly what you want.`

* 我们在使用REST接口时，接口返回的数据格式、数据类型都是后端预先定义好的，如果返回的数据格式并不是调用者所期望的，作为前端的我们可以通过以下两种方式来解决问题：

* `和后端沟通，改接口（更改数据源）`
* `自己做一些适配工作（处理数据源）`
* 一般如果是个人项目，改后端接口这种事情可以随意搞，但是如果是公司项目，改后端接口往往是一件比较敏感的事情，尤其是对于三端（web、andriod、ios）公用同一套后端接口的情况。大部分情况下，均是按第二种方式来解决问题的。

* 因此如果接口的返回值，可以通过某种手段，从静态变为动态，即调用者来声明接口返回什么数据，很大程度上可以进一步解耦前后端的关联。

* 在GraphQL中，我们通过预先定义一张Schema和声明一些Type来达到上面提及的效果，我们需要知道：

* `对于数据模型的抽象是通过Type来描述的`
* `对于接口获取数据的逻辑是通过Schema来描述的`


## 二、为什么要使用GraphQL?
## 三、GraphQL尝尝鲜——(GraphQL简单例子)

### 参考文档
https://graphql.cn/

https://blog.csdn.net/qq_41882147/article/details/82966783

https://jerryzou.com/posts/10-questions-about-graphql/

https://juejin.im/post/5a49e5ccf265da430d585cfd

https://github.com/naihe138/GraphQL-demo

