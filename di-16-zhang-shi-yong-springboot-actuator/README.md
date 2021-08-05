# 第 16 章 使用 SpringBoot Actuator

本章内容

* 在 Spring Boot 项目中启用 Actuator
* 探索 Actuator 的 endpoint
* 定制 Actuator
* 为 Actuator 增加安全机制

您有没有猜过，一个礼物包装盒里面到底是什么礼物呢？摇一摇、掂一掂、量一下尺寸，可能对里面的东西猜个八九不离十。但是直到打开它之前，是没有办法百分百的确定的。

一个正在运行的应用程序，就像一份包装好的礼物。您可以访问它，然后根据反馈做出相对合理的猜测。但您能保证一定猜测正确吗？如果能有什么办法，能让您看到应用程序的内部：查看是如何运行的、查看它的健康状况，甚至触发一些操作以改变其运行方式，那就可以确定应用程序的实际情况了！

在本章中，我们将探讨 Spring Boot 的 Actuator。Actuator 可以对 Spring Boot 应用程序进行监视，并提供一些度量指标。这些都是可以应用于生产环境的特性。 Actuator 的特征通过几个 endpoint 提供，这些 endpoint 可通过 HTTP 和 JMX mbean 获得。本章主要关注 HTTP endpoint，而在第 19 章讨论 JMX endpoint。

