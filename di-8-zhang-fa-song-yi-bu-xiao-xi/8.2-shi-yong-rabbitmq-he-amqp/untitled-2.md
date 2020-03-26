# 8.2.1 添加 RabbitMQ 到 Spring 中

在开始使用 Spring 发送和接收 RabbitMQ 消息之前，需要将 Spring Boot 的 AMQP starter 依赖项添加到构建中，以取代在前一节中添加的 Artemis 或 ActiveMQ starter：

```markup
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

将 AMQP starter 添加到构建中将触发自动配置，该配置将创建 AMQP 连接工厂和 RabbitTemplate bean，以及其他支持组件。只需添加此依赖项，就可以开始使用 Spring 从 RabbitMQ broker 发送和接收消息，表 8.4 中列出了一些有用的属性。

**表 8.4 配置 RabbitMQ broker 的位置和凭据的属性**

| 属性 | 描述 |
| :--- | :--- |
| spring.rabbitmq.addresses | 一个逗号分隔的 RabbitMQ Broker 地址列表 |
| spring.rabbitmq.host | Broker 主机（默认为 localhost） |
| spring.rabbitmq.port | Broker 端口（默认为 5672） |
| spring.rabbitmq.username | 访问 Broker 的用户名（可选） |
| spring.rabbitmq.password | 访问 Broker 的密码（可选） |

出于开发目的，可能有一个 RabbitMQ Broker，它不需要在本地机器上运行身份验证，监听端口 5672。当还在开发阶段时，这些属性可能不会有太大的用处，但是当应用程序进入生产环境时，它们无疑会很有用。

例如，假设在进入生产环境时，RabbitMQ Broker 位于一个名为 rabbit.tacocloud.com 的服务器上，监听端口 5673，并需要凭据。在这种情况下，应用程序中的以下配置。当 prod 配置文件处于活动状态时，yml 文件将设置这些属性：

```yaml
spring:
  profiles: prod
  rabbitmq:
    host: rabbit.tacocloud.com
    port: 5673
    username: tacoweb
    password: l3tm31n
```

现在 RabbitMQ 被配置到了应用程序中了，是时候使用 RabbitTemplate 发送消息了。

