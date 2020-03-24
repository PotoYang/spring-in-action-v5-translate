# 8.2.3 从 RabbitMQ 接收消息

使用 RabbitTemplate 发送消息与使用 JmsTemplate 发送消息差别不大。事实证明，从 RabbitMQ 队列接收消息与从 JMS 接收消息并没有太大的不同。

与 JMS 一样，有两个选择：

* 使用 RabbitTemplate 从队列中拉取消息
* 获取被推送到 @RabbitListener 注解的方法中的消息

让我们从基于拉模型的 RabbitTemplate.receive\(\) 方法开始。

**使用 RabbitTemplate 接收消息**

RabbitTemplate 有多个从队列中拉取消息的方法，一部分最有用的方法如下所示：

```java
// 接收消息
Message receive() throws AmqpException;
Message receive(String queueName) throws AmqpException;
Message receive(long timeoutMillis) throws AmqpException;
Message receive(String queueName, long timeoutMillis) throws AmqpException;
​
// 接收从消息转换过来的对象
Object receiveAndConvert() throws AmqpException;
Object receiveAndConvert(String queueName) throws AmqpException;
Object receiveAndConvert(long timeoutMillis) throws AmqpException;
Object receiveAndConvert(String queueName, long timeoutMillis) throws AmqpException;
​
// 接收从消息转换过来的类型安全的对象
<T> T receiveAndConvert(ParameterizedTypeReference<T> type) throws AmqpException;
<T> T receiveAndConvert(String queueName, ParameterizedTypeReference<T> type) throws AmqpException;
<T> T receiveAndConvert(long timeoutMillis, ParameterizedTypeReference<T> type) throws AmqpException;
<T> T receiveAndConvert(String queueName, long timeoutMillis, ParameterizedTypeReference<T> type) throws AmqpException;
```

这些方法是前面描述的 send\(\) 和 convertAndSend\(\) 方法的镜像。send\(\) 用于发送原始 Message 对象，而 receive\(\) 从队列接收原始 Message 对象。同样地，receiveAndConvert\(\) 接收消息，并在返回消息之前使用消息转换器将其转换为域对象。

但是在方法签名方面有一些明显的区别。首先，这些方法都不以交换键或路由键作为参数。这是因为交换和路由键用于将消息路由到队列，但是一旦消息在队列中，它们的下一个目的地就是将消息从队列中取出的使用者。使用应用程序不需要关心交换或路由键，队列是在消费应用程序是仅仅需要知道一个东西。

许多方法接受一个 long 参数来表示接收消息的超时。默认情况下，接收超时为 0 毫秒。也就是说，对 receive\(\) 的调用将立即返回，如果没有可用的消息，则可能返回空值。这与 receive\(\) 方法在 JmsTemplate 中的行为有明显的不同。通过传入超时值，可以让 receive\(\) 和 receiveAndConvert\(\) 方法阻塞，直到消息到达或超时过期。但是，即使使用非零超时，代码也要准备好处理返回的 null 值。

让我们看看如何将其付诸实践。下面程序清单显示了 OrderReceiver 的一个新的基于 Rabbit 的实现，它使用 RabbitTemplate 来接收订单。**程序清单 8.6 使用 RabbitTemplate 从 RabbitMQ 拉取订单**

```java
package tacos.kitchen.messaging.rabbit;
​
import org.springframework.amqp.core.Message;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.amqp.support.converter.MessageConverter;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
​
@Component
public class RabbitOrderReceiver {
    
    private RabbitTemplate rabbit;
    private MessageConverter converter;
    
    @Autowired
    public RabbitOrderReceiver(RabbitTemplate rabbit) {
        this.rabbit = rabbit;
        this.converter = rabbit.getMessageConverter();
    }
    
    public Order receiveOrder() {
        Message message = rabbit.receive("tacocloud.orders");
        return message != null
            ? (Order) converter.fromMessage(message)
            : null;
    }
}
```

receiveOrder\(\) 方法是所有操作发生的地方。它调用所注入的 RabbitTemplate 上的 receive\(\) 方法来从 tacocloud.queue 中获取订单。它不提供超时值，因此只能假设调用立即返回 Message 或 null。如果返回一条 Message，则使用 RabbitTemplate 中的 MessageConverter 将 Message 转换为 Order。另一方面，如果 receive\(\) 返回 null，则返回 null。

根据实际情况的不同，可能容忍一个小的延迟。例如，在 Taco Cloud 厨房项目的头顶显示器中，如果没有订单信息出现，可以等待一下，可以决定等 30 秒后再放弃。然后，可以将 receiveOrder\(\) 方法更改为传递一个 30,000 毫秒的延迟后再调用 receive\(\)：

```java
public Order receiveOrder() {
    Message message = rabbit.receive("tacocloud.order.queue", 30000);
    
    return message != null
        ? (Order) converter.fromMessage(message)
        : null;
}
```

如果你和我一样，看到这样一个硬编码的数字会让你有点不舒服。那么创建一个带 @ConfigurationProperties 注解的类是个好想法，这样就可以使用 Spring Boot 的配置属性来配置超时。如果不是 Spring Boot 已经提供了这样的配置属性，我也会觉得硬编码的数字很不舒服。如果希望通过配置设置超时，只需删除 receive\(\) 调用中的超时值，并在配置中使用 spring.rabbitmq.template.receive-timeout 属性设置它：

```yaml
spring:
  rabbitmq:
    template:
      receive-timeout: 30000
```

回到 receiveOrder\(\) 方法，请注意，必须使用 RabbitTemplate 中的消息转换器来将传入 Message 对象转换为 Order 对象。但是如果 RabbitTemplate 携带了一个消息转换器，为什么它不能进行转换呢？这正是 receiveAndConvert\(\) 方法的用途。使用 receiveAndConvert\(\)，可以像这样重写 receiveOrder\(\)：

```java
public Oreder receiveOrder() {
    return (Order) rabbit.receiveAndConvert("tacocloud.order.queue");
}
```

那就简单多了，不是吗？所看到的唯一麻烦的事情就是从 Object 到 Order 的转换。不过，除了演员阵容，还有另一种选择。相反，你可以传递一个 ParameterizedTypeReference 来直接接收一个 Order 对象：

```java
public Order receiveOrder() {
    return rabbit.receiveAndConvert("tacocloud.order.queue",
               new ParameterizedTypeReference<Order>() {});
}
```

这是否比类型转换更好还值得商榷，但它是一种比类型转换更安全的方法。使用 receiveAndConvert\(\) 的 ParameterizedTypeReference 的惟一要求是消息转换器必须是 SmartMessageConverter 的实现；Jackson2JsonMessageConverter 是唯一可以选择的开箱即用的实现。

JmsTemplate 提供的拉模型适用于许多用例，但通常最好有监听消息并在消息到达时调用的代码。让我们看看如何编写响应 RabbitMQ 消息的消息驱动 bean。

**使用监听器处理 RabbitMQ 消息**

对于消息驱动的 RabbitMQ bean，Spring 提供了 RabbitListener，相当于 RabbitMQ 中的 JmsListener。要指定当消息到达 RabbitMQ 队列时应该调用某个方法，请在相应的 bean 方法上使用 @RabbitTemplate 进行注解 。

例如，下面的程序清单显示了 OrderReceiver 的 RabbitMQ 实现，它被注解为监听订单消息，而不是使用 RabbitTemplate 来轮询订单消息。

{% code title="程序清单 8.7 声明一个方法作为 RabbitMQ 消息监听器" %}
```java
package tacos.kitchen.messaging.rabbit.listener;
​
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
​
@Component
public class OrderListener {
    
    private KitchenUI ui;
    
    @Autowired
    public OrderListener(KitchenUI ui) {
        this.ui = ui;
    }
    
    @RabbitListener(queues = "tacocloud.order.queue")
    public void receiveOrder(Order order) {
        ui.displayOrder(order);
    }
}
```
{% endcode %}

这与程序清单 8.4 中的代码非常相似。实际上，唯一改变的是监听器注解—从 @JmsListener 变为了 @RabbitListener。尽管 @RabbitListener 非常棒，但这种近乎复制的代码让我对 @RabbitListener 没什么可说的，而我之前还没有对 @JmsListener 说过。它们都非常适合编写从各自的 broker 推送给它们的消息的代码 —— JMS broker 用于 @JmsListener，RabbitMQ broker 用于 @RabbitListener。

虽然在前面的段落中可能感觉到了 @RabbitListener 不是那么让人兴奋。事实上，@RabbitListener 与 @JmsListener 的工作方式非常相似，这一点非常令人兴奋！这意味着在使用 RabbitMQ 与 Artemis 或 ActiveMQ 时，不需要学习完全不同的编程模型。同样令人兴奋的是 RabbitTemplate 和 JmsTemplate 之间的相似性。

在结束本章时，让我们继续关注 Spring 支持的另一个消息传递中间件：Apache Kafka。

