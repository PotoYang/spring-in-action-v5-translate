# 16.1 介绍 Actuator

在机器设备中， Actuator 指驱动器，是负责控制和移动机器的机械装置。在 Spring Boot 应用程序中，Spring Boot Actuator 扮演同样的角色，使我们能够看到正在运行的应用程序的内部。在某种程度上，控制应用程序的行为。

使用 Actuator 公开的 endpoint，我们可以获取正在运行的 Spring Boot 应用程序的一些情况：

* 应用程序环境中有哪些配置属性可用？
* 应用程序中各种包的日志记录级别是什么？
* 应用程序现在消耗了多少内存？
* 给定的 HTTP endpoint 被请求了多少次？
* 应用程序及其协作的其他服务的健康状况如何？

要在 Spring Boot 应用程序中启用 Actuator，只需添加 Actuator 的依赖。在 Spring Boot 应用程序 pom.xml 文件中，添加如下部分：

```markup
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

一旦 Actuator 依赖添加到了项目构建中，应用程序就有了几个现成的 endpoint，包括表 16.1 中所描述的这些。

**表 16.1：窥视 Spring Boot 应用程序内部，并操纵其运行状态的 Actuator endpoint**

| HTTP 方法 | 路径 | 描述 | 是否默认启用 |
| :--- | :--- | :--- | :--- |
| GET | /auditevents | 生成已触发的任何审核事件的报告。 | 否 |
| GET | /beans | 描述 Spring 应用程序上下文中的所有 bean。 | 否 |
| GET | /conditions | 对于在应用程序上下文中创建的 bean，自动配置条件校验通过或失败的报告。 | 否 |
| GET | /configprops | 描述所有配置属性以及当前值。 | 否 |
| GET, POST, DELETE | /env | 生成 Spring 应用程序可用的所有属性源及其属性的报告。 | 否 |
| GET | /env/{toMatch} | 描述单个环境属性的值。 | 否 |
| GET | /health | 返回应用程序的聚合运行状况和（可能）外部相关应用程序的运行状况。 | 是 |
| GET | /heapdump | 下载堆转储信息。 | 否 |
| GET | /httptrace | 生成最近 100 个请求的跟踪记录。 | 否 |
| GET | /info | 返回开发人员自定义的应用程序信息。 | 是 |
| GET | /loggers | 生成应用程序中包的列表，及其配置的有效日志级别。 | 否 |
| GET，POST | /loggers/{name} | 返回某个 logger 的有效日志记录级别，并可以通过 POST 请求设置日志级别。 | 否 |
| GET | /mappings | 生成关于所有 HTTP 映射及其相应处理方法的报告。 | 否 |
| GET | /metrics | 返回所有度量指标类别的列表。 | 否 |
| GET | /metrics/{name} | 返回某度量的多维值的集合。 | 否 |
| GET | /scheduledtasks | 列出所有计划任务 | 否。 |
| GET | /threaddump | 返回所有应用程序线程的报告。 | 否 |

除了基于 HTTP 的 endpoint，表 16.1 中的所有 Actuator 的 endpoint，除 /heapdump 以外，同时暴露为 JMX MBean。我们将在第 19 章讨论 JMX 的 Actuator。

