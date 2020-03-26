# 8.2.2 使用 RabbitTemplate 发送消息

Spring 对于 RabbitMQ 消息支持的核心就是 RabbitTemplate。RabbitTemplate 提供一套与 JmsTemplate 类似的方法。但是对于 RabbitMQ，在工作方式上还是有一些细微的差别。

关于使用 RabbitTemplate 发送消息，send\(\) 和 convertAndSend\(\) 方法与来自 JmsTemplate 的同名方法并行。但是不同于 JmsTemplate 方法，它只将消息路由到给定的队列或主题，RabbitTemplate 方法根据交换和路由键发送消息。下面是一些用 RabbitTemplate 发送消息的最有用的方法：

```java
// 发送原始消息
void send(Message message) throws AmqpException;
void send(String routingKey, Message message) throws AmqpException;
void send(String exchange, String routingKey, Message message) throws AmqpException;
​
// 发送从对象转换过来的消息
void convertAndSend(Object message) throws AmqpException;
void convertAndSend(String routingKey, Object message) throws AmqpException;
void convertAndSend(String exchange, String routingKey, Object message) throws AmqpException;
​
// 发送经过处理后从对象转换过来的消息
void convertAndSend(Object message, MessagePostProcessor mPP) throws AmqpException;
void convertAndSend(String routingKey, Object message, MessagePostProcessor messagePostProcessor) throws AmqpException;
void convertAndSend(String exchange, String routingKey, Object message, MessagePostProcessor messagePostProcessor) throws AmqpException;
```

这些方法与 JmsTemplate 中的孪生方法遵循类似的模式。前三个 send\(\) 方法都发送一个原始消息对象。接下来的三个 convertAndSend\(\) 方法接受一个对象，该对象将在发送之前在后台转换为消息。最后三个 convertAndSend\(\) 方法与前三个方法类似，但是它们接受一个 MessagePostProcessor，可以在消息对象发送到代理之前使用它来操作消息对象。

这些方法与对应的 JmsTemplate 方法不同，它们接受 String 值来指定交换和路由键，而不是目的地名称（或 Destination 对象\)。不接受交换的方法将把它们的消息发送到默认交换。同样，不接受路由键的方法将使用默认路由键路由其消息。

让我们用 RabbitTemplate 发送 taco 订单。一种方法是使用 send\(\) 方法，如程序清单 8.5 所示。但是在调用 send\(\) 之前，需要将 Order 对象转换为消息。如果不是因为 RabbitTemplate 使用 getMessageConverter\(\) 方法使其消息转换器可用，这可能是一项乏味的工作。**程序清单 8.5 使用 RabbitTemplate.send\(\) 发送消息**

```java
package tacos.messaging;
​
import org.springframework.amqp.core.Message;
import org.springframework.amqp.core.MessageProperties;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.amqp.support.converter.MessageConverter;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import tacos.Order;
​
@Service
public class RabbitOrderMessagingService implements OrderMessagingService {
    
    private RabbitTemplate rabbit;
    
    @Autowired
    public RabbitOrderMessagingService(RabbitTemplate rabbit) {
        this.rabbit = rabbit;
    }
    
    public void sendOrder(Order order) {
        MessageConverter converter = rabbit.getMessageConverter();
        MessageProperties props = new MessageProperties();
        Message message = converter.toMessage(order, props);
        rabbit.send("tacocloud.order", message);
    }
}
```

有了 MessageConverter 之后，将 Order 转换为消息就很简单了。必须使用 MessageProperties 提供任何消息属性，但是如果不需要设置任何此类属性，则可以使用 MessageProperties 的缺省实例。然后，剩下的就是调用 send\(\)，将交换键和路由键（两者都是可选的）与消息一起传递。在本例中，只指定了与消息一起的路由键：tacocloud.order，因此将使用缺省交换。

说到默认交换，默认交换名称是 “”（一个空 String ），它对应于 RabbitMQ Broker 自动创建的默认交换。同样，默认的路由键是 “”（其路由取决于所涉及的交换和绑定）。可以通过设置 spring.rabbitmq.template.exchange 和 spring.rabbitmq.template.routing-key 属性来覆盖这些缺省值：

```yaml
spring:
  rabbitmq:
    template:
      exchange: tacocloud.orders
      routing-key: kitchens.central
```

在这种情况下，所有发送的消息都将自动发送到名为 tacocloud.orders 的交换器。如果在 send\(\) 或 convertAndSend\(\) 调用中也未指定路由键，则消息将有一个 kitchens.central 的路由键。

从消息转换器创建消息对象非常简单，但是使用 convertAndSend\(\) 让 RabbitTemplate 处理所有的转换工作就更容易了：

```java
public void sendOrder(Order order) {
    rabbit.convertAndSend("tacocloud.order", order);
}
```

**配置消息转换器**

默认情况下，使用 SimpleMessageConverter 执行消息转换，SimpleMessageConverter 能够将简单类型（如 String）和可序列化对象转换为消息对象。但是 Spring 为 RabbitTemplate 提供了几个消息转换器，包括以下内容：

* Jackson2JsonMessageConverter —— 使用Jackson 2 JSON处理器将对象与 JSON 进行转换
* MarshallingMessageConverter —— 使用 Spring 的序列化和反序列化抽象转换 String 和任何类型的本地对象
* SimpleMessageConverter —— 转换 String、字节数组和序列化类型
* ContentTypeDelegatingMessageConverter —— 基于 contentType 头信息将对象委托给另一个 MessageConverter
* MessagingMessageConverter —— 将消息转换委托给底层 MessageConverter，将消息头委托给 AmqpHeaderConverter

如果需要修改消息转换器，需要做的是配置 MessageConverter bean，例如，对于基于 JSON 的消息对话，可以像下面这样配置 Jackson2JsonMessageConverter：

```java
@Bean
public MessageConverter messageConverter() {
    return new Jackson2JsonMessageConverter();
}
```

Spring Boot 的自动配置将会发现这个 bean 并 RabbitTemplate 的缺省的消息转换器那里。

**设置消息属性**

与 JMS 一样，可能需要在发送的消息中设置一些标题。例如，假设需要为通过 Taco Cloud 网站提交的所有订单发送一个 X\_ORDER\_SOURCE。在创建 Message 对象时，可以通过提供给消息转换器的 MessageProperties 实例设置消息头。

重新访问程序清单 8.5 中的 sendOrder\(\) 方法，只需要添加一行代码来设置标题：

```java
public void sendOrder(Order order) {
    MessageConverter converter = rabbit.getMessageConverter();
    MessageProperties props = new MessageProperties();
    props.setHeader("X_ORDER_SOURCE", "WEB");
    Message message = converter.toMessage(order, props);
    rabbit.send("tacocloud.order", message);
}
```

但是，在使用 convertAndSend\(\) 时，不能快速访问 MessageProperties 对象。不过，MessagePostProcessor 可以做到这一点：

```java
@Override
public void sendOrder(Order order) {
    rabbit.convertAndSend("tacocloud.order.queue", order,
         new MessagePostProcessor() {
             @Override
             public Message postProcessMessage(Message message)
                 throws AmqpException {
                 MessageProperties props = message.getMessageProperties();
                 props.setHeader("X_ORDER_SOURCE", "WEB");
                 return message;
             }
         });
}
```

这里，在 convertAndSend\(\) 中使用 MessagePostProcessor 的匿名内部类进行实现 。在 postProcessMessage\(\) 方法中，首先从消息中获取 MessageProperties，然后调用 setHeader\(\) 来设置 X\_ORDER\_SOURCE 头信息。

现在已经了解了如何使用 RabbitTemplate 发送消息，接下来让我们将注意力转移到从 RabbitMQ 队列接收消息的代码上。

