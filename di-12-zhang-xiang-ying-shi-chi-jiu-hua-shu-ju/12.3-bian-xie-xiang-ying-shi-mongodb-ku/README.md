# 12.3 编写响应式 MongoDB 库

MongoDB 是另一个著名的 NoSQL 数据库。Cassandra 是一个行存储数据库，MongoDB 一般被认为是一个文档数据库。更具体地说，MongoDB 以 BSON（Binary JSON）格式存储文档数据，并且提供了大致类似于其他数据库的数据查询方式。

与 Cassandra 一样，关键一点是理解 MongoDB 不是关系型数据库。管理 MongoDB 服务器集群，以及为 MongoDB 进行数据建模，都需要与处理其他类型数据库不同的思维方式。

与使用 Spring Data 结合 JPA 或 Cassandra 一样，使用 MongoDB 结合 Spring Data 也没有什么显著的不同。您需要使用注解，将实体类型映射到文档结构上。您将编写同样遵循相同编程模型的接口。当然，在做这些事情之前，您必须在项目中启用 Spring Data MongoDB。

