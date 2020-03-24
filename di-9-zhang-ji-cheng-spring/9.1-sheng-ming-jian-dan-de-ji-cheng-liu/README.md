# 9.1 声明简单的集成流

一般来说，Spring Integration 支持创建集成流，应用程序可以通过这些集成流接收或发送数据到应用程序本身的外部资源。应用程序可以与之集成的一种资源是文件系统。因此，在 Spring Integration 的众多组件中，有用于读写文件的通道适配器。

为了熟悉 Spring Integration，将创建一个向文件系统写入数据的集成流。首先，需要将 Spring Integration 添加到项目构建中。对于 Maven，必要的依赖关系如下：

```markup
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-integration</artifactId>
</dependency>
​
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-integration-file</artifactId>
</dependency>
```

第一个依赖项是 Spring Integration 的 Spring Boot starter。无论 Spring Integration 流可能与什么集成，这种依赖关系都是开发 Spring Integration 流所必需的。与所有 Spring Boot starter 依赖项一样，它也存在于 Initializr 的复选框表单中。

第二个依赖项是 Spring Integration 的文件端点模块。此模块是用于与外部系统集成的二十多个端点模块之一。我们将在第 9.2.9 节中更多地讨论端点模块。但是，就目前而言，要知道文件端点模块提供了将文件从文件系统提取到集成流或将数据从流写入文件系统的能力。

接下来，需要为应用程序创建一种将数据发送到集成流的方法，以便将数据写入文件。为此，将创建一个网关接口，如下面所示。

{% code title="程序清单 9.1 消息网关接口，用于将方法转换为消息" %}
```java
package sia5;
​
import org.springframework.integration.annotation.MessagingGateway;
import org.springframework.integration.file.FileHeaders;
import org.springframework.messaging.handler.annotation.Header;
​
@MessagingGateway(defaultRequestChannel="textInChannel")
public interface FileWriterGateway {
    void writeToFile(
        @Header(FileHeaders.FILENAME) String filename,
        String data);
}
```
{% endcode %}

尽管它是一个简单的 Java 接口，但是关于 FileWriterGateway 还有很多要说的。首先会注意到它是由 @MessagingGateway 注解的。这个注解告诉 Spring Integration 在运行时生成这个接口的实现 —— 类似于 Spring Data 如何自动生成存储库接口的实现。当需要编写文件时，代码的其他部分将使用这个接口。

@MessagingGateway 的 defaultRequestChannel 属性表示，对接口方法的调用产生的任何消息都应该发送到给定的消息通道。在本例中，声明从 writeToFile\(\) 调用产生的任何消息都应该发送到名为 textInChannel 的通道。

对于 writeToFile\(\) 方法，它接受一个文件名作为字符串，另一个字符串包含应该写入文件的文本。关于此方法签名，值得注意的是 filename 参数使用 @Header 进行了注解。在本例中，@Header 注解指示传递给 filename 的值应该放在消息头中（指定为 FileHeaders），解析为 file\_name 的文件名，而不是在消息有效负载中。另一方面，数据参数值则包含在消息有效负载中。

现在已经有了一个消息网关，还需要配置集成流。尽管添加到构建中的 Spring Integration starter 依赖项支持 Spring Integration 的基本自动配置，但仍然需要编写额外的配置来定义满足应用程序需求的流。声明集成流的三个配置选项包括：

* XML 配置
* Java 配置
* 使用 DSL 进行 Java 配置

我们将对 Spring Integration 的这三种风格的配置进行讲解，从最原始的 XML 配置开始。

