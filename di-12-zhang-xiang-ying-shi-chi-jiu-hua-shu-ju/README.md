# 第 12 章 响应式持久化数据

本章介绍

* Spring Data 中的响应式 Repository
* 编写适用于 Cassandra 和 MongoDB 的响应式 Repository
* 非响应式 Repository 适配响应式使用
* 基于 Cassandra 的数据建模

当我考虑非阻塞响应式代码和阻塞式代码时，我开始想到交通的早高峰。早高峰时每个人都匆忙的赶往自己的目的地，导致人们都无可奈何的拥挤在一起。每个人都明白，如果不是路上的其他人，我肯定可以按时到达目的地。

即使我着急去某个地方（非阻塞的，我没有其他事要做），那也不是说路上就会一路畅通。可能前面的其他驾驶人发生了一次小车祸，挡住了道路。所以即使我回家这个事是非阻塞的，在事故现场清理之前我还是被堵在原地。

在上一章中，您了解了如何使用 Spring WebFlux 创建响应式地、无阻塞的 Controller，这有助于提高 web 层的可伸缩性。但是那些 Controller 只有在与其他非阻塞组件一起工作时，才是真正的无阻塞。如果我们使用 Spring WebFlux 编写的 Controller，仍然依赖于阻塞的 Repository，那我们的响应式 Controller 将会由于等待生成数据而被阻止。

因此，重要的是整个数据流，一直从 Controller 到 Repository，都是响应式和非阻塞的。在本章中，您将了解如何使用 Spring Data 编写响应式 Repository，这类似于在第三章中的编程模型。我们将首先调查研究一下 Spring Data 对响应式有哪些支持。

