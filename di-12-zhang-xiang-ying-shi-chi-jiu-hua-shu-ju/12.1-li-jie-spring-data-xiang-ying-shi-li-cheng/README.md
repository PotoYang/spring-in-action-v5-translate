# 12.1 理解 Spring Data 响应式历程

从 Spring Data Kay 系列发布开始， Spring Data 开始提供对响应式 Repository 的支持。这包括使用 Cassandra、MongoDB、Couchbase 或 Redis 持久化数据时对响应式编程模型的支持。

> **名称的含义？**
>
> 尽管 Spring Data 项目按照自己的速度进行版本控制，但它们是在统一的发布序列中发布的，其中发布序列的每个版本都以计算机科学的重要人物命名。
>
> 这些人名按字母顺序排列，包括 Babbage、Codd、Dijkstra、Evans、Fowler、Gosling、Hopper 和 Ingalls。在写这本书时，最新发布的版本是 Spring Data Kay，这是以 Alan Kay 的名字命名的，他是 Smalltalk 编程语言的设计者。

您可能注意到，我没有提到关系型数据库或 JPA 。不幸的是，Spring Data 对响应式 JPA 没有支持。尽管关系数据库确实是业界最丰富的数据库，但使 Spring Data JPA 支持响应式编程模型，需要数据库和 JDBC 驱动程序也参与进来，一起支持响应式、非阻塞模型。可惜的是，至少现在不支持以响应式的方式处理关系数据库。希望这种情况在不久的将来得到解决。

本章重点介绍如何使用 Spring Data 来开发 Repository，当然只是为那些支持响应式编程模型的数据库类型。让我们来看一下，Spring Data 的响应式模型与非响应式模型有什么不同。

