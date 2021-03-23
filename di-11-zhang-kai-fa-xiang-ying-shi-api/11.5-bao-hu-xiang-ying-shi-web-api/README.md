# 11.5 保护响应式 web API

自从 `Spring Security`（之前广为人知的名称是`Acegi Security`）以来，web 安全模型都是围绕 servlet 过滤器构建的。毕竟，这是很明显的逻辑。为了确保请求者拥有适当的权限，拦截基于 servlet 的请求，显而易见的就是选择使用 servlet 过滤器。但是 `Spring WebFlux` 给这种方法带来了一个难题。

在使用 `Spring WebFlux` 编写 web 应用程序时，并不能保证 servlet 甚至还参与其中。事实上，一个响应式 web 应用程序，更有可能是构建在 Netty 或其他非 servlet 服务器上的。这是否意味着基于 servlet 过滤器的 `Spring Security`, 不能用于保护 `Spring WebFlux` 应用程序呢？

在保护 `Spring WebFlux` 应用程序时，确实不能选择使用 servlet 过滤器。但 `Spring Security` 仍能胜任这项任务。从 5.0.0 版开始，`Spring Security` 可以用来保护基于servlet 的 `Spring MVC` 和响应式 `Spring WebFlux` 应用程序。它使用 Spring 的WebFilter 实现这一点，这是 Spring 特有的，类似于 servlet 过滤器，但并不需要依赖 servlet API。

更值得一提的是，响应式 `Spring Security` 的配置模型与您在 `第4章` 中看到的没有太大区别。事实上，不像 `Spring WebFlux` 与 `Spring MVC` 有一个独立的依赖关系， `Spring Security` 使用和以前一样的 starter，无论您是否打算使用它来保护 `Spring MVC` 的 web 应用程序或用 `Spring WebFlux` 的应用程序。再重温一下，starter 的配置是这样的：

```markup
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

也就是说， `Spring Security` 的响应式和非响应式配置模型没什么大的区别。让我们来快速比较查看一下两种配置的模型。

