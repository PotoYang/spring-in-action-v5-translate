# 13.3 注册并发现服务

除非有服务在 Eureka 注册了，否则 Eureka 服务的注册表是没什么用的。如果您的服务将被其他服务发现和使用，那么您需要使它们成为 Eureka 注册服务的客户端。要使应用程序（任何应用程序，假设是微服务）成为 Eureka 注册服务的客户端，必须做的是，将 Eureka 客户端依赖项添加到应用程序构建配置中：

```markup
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

与 Eureka starter 服务依赖项一样，您还需要在依赖管理中设置 Spring Cloud 版本属性：

```markup
<properties>
   ...
   <spring-cloud.version>Finchley.SR1</spring-cloud.version>
</properties>

...

<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-dependencies</artifactId>
      <version>${spring-cloud.version}</version>
      <type>pom</type>
      <scope>import</scope>
      </dependency>
  </dependencies>
</dependencyManagement>
```

您可以手动将这些条目添加到应用程序的 `pom.xml` 文件中，但更简单的方法是使用 Spring Initializr 的选择框，通过勾选 `Eureka Discovery` 以添加这些依赖项。

Eureka starter 依赖项，会帮您添加使用 Eureka 发现服务所需的一切，包括 Eureka 的客户端库以及 Ribbon 负载均衡器。其他就什么也不用做了，这就使您的应用程序成为了 Eureka 服务的客户端。当应用程序启动时，它会尝试联系本地运行的 Eureka 服务并侦听端口 8761，并以 `UNKNOWN` 名称注册自己。

