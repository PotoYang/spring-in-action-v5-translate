# 15.3 管理失败事件

每次调用受断路器保护的方法时，调用信息都会生成多条数据，并发布在 HTTP 流中。该流可用于实时监视应用程序的运行状况。对每个断路器收集到的数据，Hystrix 流包括以下内容：

* 方法被调用了多少次
* 成功调用了多少次
* 降级方法被调用了多少次
* 方法超时多少次

Hystrix 流由 Actuator 接口提供。我们将在第 16 章进一步讨论 Actuator。但是，目前，需要将 Actuator 依赖添加到服务中，以启用 Hystrix 流。在 Maven pom.xml 文件中添加以下内容：

```markup
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Hystrix 流接口路径为 `/actuator/hystrix.stream` 。在默认情况下，大多数 Actuator 接口都是禁用的。如果您要启用 Hystrix 流的相关接口，需要在每个应用的 application.yml 中添加如下配置：

```yaml
management:
  endpoints:
    web:
      exposure:
        include: hystrix.stream
```

或者，将 `management:endpoints:web:exposure:include` 属性放置在 application.yml 配置中，使其作为 Config Server 为所有服务提供的全局设置。

应用程序开始暴露 Hystrix 流以后，就可以通过任意的 REST 客户端使用这份数据。但是，在你开始编写自定义的 Hystrix 流客户端之前，请注意，HTTP 流中的每个条目都包含各种 JSON 数据，需要大量的客户端工作来解释这些数据。尽管，编写自己的 Hystrix 流处理客户端，并不是一项不可能完成的任务，但您应该优先考虑使用 Hystrix 仪表板，然后再考虑开发自己的仪表盘。

