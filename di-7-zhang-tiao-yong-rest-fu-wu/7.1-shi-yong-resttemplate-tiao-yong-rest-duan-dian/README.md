# 7.1 使用 RestTemplate 调用 REST 端点

从客户的角度来看，与 REST 资源进行交互需要做很多工作 —— 主要是单调乏味的样板文件。使用低级 HTTP 库，客户端需要创建一个客户端实例和一个请求对象，执行请求，解释响应，将响应映射到域对象，并处理过程中可能抛出的任何异常。不管发送什么 HTTP 请求，所有这些样板文件都会重复。

为了避免这样的样板代码，Spring 提供了 RestTemplate。正如 JDBCTemplate 处理使用 JDBC 糟糕的那部分一样，RestTemplate 使你不必为调用 REST 资源而做单调的工作。

RestTemplate 提供了 41 个与 REST 资源交互的方法。与其检查它提供的所有方法，不如只考虑 12 个惟一的操作，每个操作都有重载，以形成 41 个方法的完整集合。表 7.1 描述了 12 种操作。

**表 7.1 RestTemplate 定义的 12 个唯一操作**

| 方法 | 描述 |
| :--- | :--- |
| delete\(...\) | 对指定 URL 上的资源执行 HTTP DELETE请求 |
| exchange\(...\) | 对 URL 执行指定的 HTTP 方法，返回一个 ResponseEntity，其中包含从响应体映射的对象 |
| execute\(...\) | 对 URL 执行指定的 HTTP 方法，返回一个映射到响应体的对象 |
| getForEntity\(...\) | 发送 HTTP GET 请求，返回一个 ResponseEntity，其中包含从响应体映射的对象 |
| getForObject\(...\) | 发送 HTTP GET 请求，返回一个映射到响应体的对象 |
| headForHeaders\(...\) | 发送 HTTP HEAD 请求，返回指定资源 URL 的 HTTP 请求头 |
| optionsForAllow\(...\) | 发送 HTTP OPTIONS 请求，返回指定 URL 的 Allow 头信息 |
| patchForObject\(...\) | 发送 HTTP PATCH 请求，返回从响应主体映射的结果对象 |
| postForEntity\(...\) | 将数据 POST 到一个 URL，返回一个 ResponseEntity，其中包含从响应体映射而来的对象 |
| postForLocation\(...\) | 将数据 POST 到一个 URL，返回新创建资源的 URL |
| postForObject\(...\) | 将数据 POST 到一个 URL，返回从响应主体映射的对象 |
| put\(...\) | 将资源数据 PUT 到指定的URL |

除了 TRACE 之外，RestTemplate 对于每个标准 HTTP 方式至少有一个方法。此外，execute\(\) 和 exchange\(\) 为使用任何 HTTP 方式发送请求提供了低层的通用方法。表 7.1 中的大多数方法都被重载为三种方法形式：

* 一种是接受一个 String 作为 URL 规范，在一个变量参数列表中指定 URL 参数。
* 一种是接受一个 String 作为 URL 规范，其中的 URL 参数在 Map&lt;String, String&gt; 中指定。
* 一种是接受 java.net.URI 作为 URL 规范，不支持参数化 URL。

一旦了解了 RestTemplate 提供的 12 个操作以及每种变体的工作方式，就可以很好地编写调用资源的 REST 客户端了。

要使用 RestTemplate，需要创建一个实例：

```java
RestTemplate rest = new RestTemplate();
```

或是将它声明为一个 bean，在需要它的时候将其注入：

```java
@Bean
public RestTemplate restTemplate() {
    return new RestTemplate();
}
```

让我们通过查看支持四种主要 HTTP 方法（GET、PUT、DELETE 和 POST）的操作来探寻 RestTemplate 的操作。我们将从 getForObject\(\) 和 getForEntity\(\) —— GET 方法开始。

