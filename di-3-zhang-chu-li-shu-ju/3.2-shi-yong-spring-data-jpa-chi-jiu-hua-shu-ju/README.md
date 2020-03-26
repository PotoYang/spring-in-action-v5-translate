# 3.2 使用 Spring Data JPA 持久化数据

Spring Data 项目是一个相当大的伞形项目，几个子项目组成，其中大多数子项目关注于具有各种不同数据库类型的数据持久化。一些最流行的 Spring 数据项目包括：

* _Spring Data JPA_ - 针对关系数据库的持久化
* _Spring Data Mongo_ - 针对 Mongo 文档数据库的持久化
* _Spring Data Neo4j_ - 针对 Neo4j 图形数据库的持久化
* _Spring Data Redis_ - 针对 Redis 键值存储的持久化
* _Spring Data Cassandra_ - 针对 Cassandra 数据库的持久化

Spring Data 为所有这些项目提供的最有意思和最有用的特性之一是能够基于存储库规范接口自动创建存储库。

为了了解 Spring Data 是如何工作的，需要将本章前面介绍的基于 jdbc 的存储库替换为 Spring Data JPA 创建的存储库。但是首先，需要将 Spring Data JPA 添加到项目构建中。

