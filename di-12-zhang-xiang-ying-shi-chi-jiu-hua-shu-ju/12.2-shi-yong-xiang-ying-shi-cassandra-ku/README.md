# 12.2 使用响应式 Cassandra 库

Cassandra 是一个分布式、高性能、高可用、最终一致、分区行存储的 NoSQL 数据库。

这些词描述了 Cassandra 的主要特点，每一个都准确的说明了 Cassandra 的一种能力。简单地说，Cassandra 以数据行的方式处理数据，这些数据被写入到表中，这些表分区后，存储到多个节点上。没有一个节点存储所有数据，且任何给定的行，都是以跨多个节点的多个复本存储的，这种方式就消除了单点故障。

Spring Data Cassandra 为 Cassandra 提供了自动 Repository 支持。这和为关系数据库提供支持的 Spring Data JPA 有很大不同。此外，Spring Data Cassandra 提供了新的注解，用于将应用程序实体类型映射到后台数据库结构上。

在我们进一步探索 Cassandra 之前，必须始终牢记一点。就是尽管 Cassandra 与 Oracle、SQL Server 等关系型数据库有许多相似的概念，但 Cassandra 不是一个关系型数据库。 Cassandra 在很多方面与关系型数据库不同，我将试图解释 Cassandra 的特质，以及通过 Spring Data 使用它。我鼓励您阅读 Cassandra [`(http://cassandra.apache.org/doc/latest/)`](http://cassandra.apache.org/doc/latest/) 的官方文档，以彻底全面的了解为什么 Cassandra 如此大受欢迎。

让我们从在 Taco Cloud 项目中启用 Spring Data Cassandra 开始。

