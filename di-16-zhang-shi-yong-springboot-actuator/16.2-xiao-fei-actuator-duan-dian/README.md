# 16.2 使用 Actuator 端点

Actuator 可以通过表 16.1 中列出的 HTTP 端点，提供一个有关正在运行的应用程序的，有趣和有用的名副其实的宝库。 作为 HTTP 端点，它们可以像任何 REST API 一样使用，使用您熟悉的任何 HTTP 客户端，包括 Spring 的 RestTemplate 和 WebClient，来自基于浏览器的 JavaScript 应用程序，或者简单地使用 curl 命令行客户端。

为了探索 Actuator 端点，您将在本章中使用 curl 命令行客户端。 在第 17 章中，我将向您介绍 Spring Boot Admin，它在应用程序的 Actuator 端点之上构建出了一个用户友好的 Web 应用程序。

为了解 Actuator 都提供了哪些端点，可以对 Actuator 基本路径发送 GET 请求，这将返回所有端点的 HATEOAS 链接。 使用 curl 向 `/actuator` 发出请求，您可能会得到类似这样的响应（为了节省篇幅而进行了删节）：

```bash
$ curl localhost:8081/actuator
{
    "_links": {
        "self": {
            "href": "http://localhost:8081/actuator",
            "templated": false
        },
        "auditevents": {
            "href": "http://localhost:8081/actuator/auditevents",
            "templated": false
        },
        "beans": {
            "href": "http://localhost:8081/actuator/beans",
            "templated": false
        },
        "health": {
            "href": "http://localhost:8081/actuator/health",
            "templated": false
        }
    },
    ...
}
```

因为不同的库可能会贡献自己额外的 Actuator 端点，并且因为某些端点可能不会被暴露，所以实际结果可能因应用程序而异。

不论差异如何，从 Actuator 的基本路径返回的这些链接，都明确展示了 Actuator 所提供的端点。让我们开始探索 Actuator, 从提供应用程序的基本信息的两个端点 `/health` 和 `/info` 开始。

