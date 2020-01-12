# Sequelize快速入门

## 一、Sequelize是什么?
* 关于Sequelize.js是一个用于Node.js的数据库ORM库，支持Postgres、MySQL/MariaDB、SQLite、SQL Server等引擎。

* ORM（Object Relational Mapping，对象关系映射），是一种为了解决面向对象与关系数据库存在的互不匹配的现象的技术，通过描述对象和数据库之间映射的元数据，把程序中的对象自动持久化到关系数据库中。
* 它的作用是在关系型数据库和对象之间作一个映射，这样，我们在具体的操作数据库的时候，就不需要再去和复杂的SQL语句打交道，只要像平时操作对象一样操作它就可以了 。



## 二、为什么要使用Sequelize?

* 难度开发难度：ORM都有完善的文档，几乎所有的操作只需要按文档调用指定方法即可，不需要自己拼接SQL
* 提升安全性：ORM会处理好SQL注入问题，不需要开发者关注
* 降低封装复杂度：公共逻辑可以基于ORM封装，非常方便
* 下文不区分“模型”和“Model”，均指Sequelize中与数据表对应的数据模型。


## 三、Sequelize简单例子
#### 在 Node 中使用 MYSQL
* 访问 MYSQL 需要通过网络请求给 MYSQL 服务器，对于 Node.js 程序，这个访问 MySQL 服务器的软件包通常称为 MySQL 驱动程序。不同的编程语言需要实现自己的驱动，MySQL 官方提供了各种多种的驱动程序。目前社区使用的比较广泛的 Mysql Nodejs 驱动程序是 mysql 和 mysql2 (与mysql的联系：兼容 mysql 大多数 API 以及性能更加优异)
* node-mysql 是一个实现了 MySQL 协议的 Node.js JavaScript 客户端，通过这个模块可以与 MySQL 数据库建立连接、执行查询等操作。如果直接使用 mysql 包提供的接口进行操作，编写的代码就比较底层，基本类同直接的 sql，例如查询操作




## 四、参考文档

https://juejin.im/post/5c259cf46fb9a049eb3bff49

https://github.com/demopark/sequelize-docs-Zh-CN

https://zhuanlan.zhihu.com/p/91195711
