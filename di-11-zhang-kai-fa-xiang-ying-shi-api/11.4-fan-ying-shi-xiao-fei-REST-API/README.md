# 11.4 响应式消费 REST API

在第七章，你已经使用过 RestTemplate 通过客户端向 Taco Cloud API 发出请求。RestTemplate 是一个老组件了，引入自 Spring 3.0 版本。至今为止，我们使用它向应用程序发出过无数次请求。

但是 RestTemplate 提供的所有处理方法都不属于反应式的类型和领域。这就意味着如果你想以反应式的方式处理一个响应的数据 ，你必须用 Flux 或 Mono 去包装它。如果你想用 Post 或者 Put 方法发送已有的 Flux 或 Mono 请求，你需要将先数据抽取出来再放入一个非响应式的类。

所以，能够有一种办法使用原生的 RestTemplate 去处理反应式类型就很棒了。不用担心，Spring 5 提供了 WebClient 作为 RestTemplate 的反应式版本。当 WebClient 向外部 APIs 发出请求的同时，也可以发出或接收反应式类型。

使用 WebClient 和使用 RestTemplate 是完全不一样的体验。RestTemplate 会使用不同的方法处理不同类型的请求，而 WebClient 拥有了一个流畅的构建者风格接口（builderstyle interface），可以让你描述并发送请求。WebClient 的常用使用方法有以下几种：

- 创建一个 WebClient 实例（或者注入一个 WebClient Bean）
- 指定发送请求的 HTTP 方法
- 指定请求中所必要的 URI 和 header
- 提交请求
- 消费响应

接下来让我们看几个 WebClient 实战的例子，开始了解如何使用 WebClient 来发送 HTTP GET 请求。

