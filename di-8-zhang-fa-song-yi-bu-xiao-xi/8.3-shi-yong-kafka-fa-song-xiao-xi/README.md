# 8.3 使用 Kafka 发送消息

Apache Kafka 是我们在本章中研究的最新消息传递选项。乍一看，Kafka 是一个消息代理，就像ActiveMQ、Artemis 或 Rabbit 一样。但是 Kafka 有一些独特的技巧。

Kafka 被设计为在集群中运行，提供了巨大的可伸缩性。通过将其 topic 划分到集群中的所有实例中，它具有很强的弹性。RabbitMQ 主要处理 exchange 中的队列，而 Kafka 仅利用 topic 来提供消息的发布/订阅。

Kafka topic 被复制到集群中的所有 broker 中。集群中的每个节点充当一个或多个 topic 的 leader，负责该 topic 的数据并将其复制到集群中的其他节点。

更进一步说，每个 topic 可以分成多个分区。在这种情况下，集群中的每个节点都是一个 topic 的一个或多个分区的 leader，但不是整个 topic 的 leader。该 topic 的职责由所有节点分担。图 8.2 说明了这是如何工作的。

![&#x56FE; 8.2 Kafka &#x96C6;&#x7FA4;&#x7531;&#x591A;&#x4E2A; broker &#x7EC4;&#x6210;&#xFF0C;&#x6BCF;&#x4E00;&#x4E2A;&#x90FD;&#x4F5C;&#x4E3A; topic &#x5206;&#x533A;&#x7684; leader](../../.gitbook/assets/图%208.2.jpg)

由于 Kafka 独特的构建风格，我鼓励你在迪伦·斯科特（Dylan Scott，2017）的_《Kafka 实战》_中阅读更多关于它的内容。出于我们的目的，我们将重点讨论如何使用 Spring 向 Kafka 发送和接收消息。

