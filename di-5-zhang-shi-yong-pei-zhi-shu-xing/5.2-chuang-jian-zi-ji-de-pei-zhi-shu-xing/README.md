# 5.2 创建自己的配置属性

正如前面提到的，配置属性只不过是指定来接受 Spring 环境抽象配置的 bean 的属性。没有提到的是如何指定这些 bean 来使用这些配置。

为了支持配置属性的属性注入，Spring Boot 提供了@ConfigurationProperties 注释。当放置在任何 Spring bean 上时，它指定可以从 Spring 环境中的属性注入到该 bean 的属性。

为了演示 @ConfigurationProperties 是如何工作的，假设已经将以下方法添加到 OrderController 中，以列出经过身份验证的用户之前的订单：

```java
@GetMapping
public String ordersForUser(
    @AuthenticationPrincipal User user, Model model) {
    model.addAttribute("orders",
        orderRepo.findByUserOrderByPlaceAtDesc(user));
    
    return "orderList";
}
```

除此之外，还需要向 OrderRepository 添加了必要的 findByUser\(\) 方法：

```java
List<Order> findByUserOrderByPlaceAtDesc(User user);
```

请注意，此存储库方法是用 OrderByPlacedAtDesc 子句命名的。OrderBy 部分指定一个属性，通过该属性对结果排序 —— 在本例中是 placedAt 属性。最后的 Desc 让排序按降序进行。因此，返回的订单列表将按时间倒序排序。

如前所述，在用户下了一些订单之后，这个控制器方法可能会很有用。但对于最狂热的 taco 鉴赏家来说，它可能会变得有点笨拙。在浏览器中显示的一些命令是有用的；一长串没完没了的订单只是噪音。假设希望将显示的订单数量限制为最近的 20 个订单，可以更改 ordersForUser\(\)：

```java
@GetMapping
public String ordersForUser(
    @AuthenticationPrincipal User user, Model model) {
    Pageable pageable = PageRequest.of(0, 20);
    model.addAttribute("orders",
        orderRepo.findByUserOrderByPlaceAtDesc(user));
    
    return "orderList";
}
```

随着这个改变，OrderRepository 跟着需要变为：

```java
List<Order> findByUserOrderByPlaceAtDesc(User user, Pageable pageable);
```

这里，已经更改了 findByUserOrderByPlacedAtDesc\(\) 方法的签名，以接受可分页的参数。可分页是 Spring Data 通过页码和页面大小选择结果子集的方式。在 ordersForUser\(\) 控制器方法中，构建了一个 PageRequest 对象，该对象实现了 Pageable 来请求第一个页面（page zero），页面大小为 20，以便为用户获得最多 20 个最近下的订单。

虽然这工作得非常好，但它让我感到有点不安，因为已经硬编码了页面大小。如果后来发现 20 个订单太多，而决定将其更改为 10 个订单，该怎么办？因为它是硬编码的，所以必须重新构建和重新部署应用程序。

可以使用自定义配置属性来设置页面大小，而不是硬编码页面大小。首先，需要向 OrderController 添加一个名为 pageSize 的新属性，然后在 OrderController 上使用 @ConfigurationProperties 注解 ，如下面的程序清单所示。

{% code title="程序清单 5.1 在 OrderController 中使用配置属性" %}
```java
@Controller
@RequestMapping("/orders")
@SessionAttributes("order")
@ConfigurationProperties(prefix="taco.orders")
public class OrderController {
    
    private int pageSize = 20;
    
    public void setPageSize(int pageSize) {
        this.pageSize = pageSize;
    }
    
    ...
    
    @GetMapping
    public String ordersForUser(
        @AuthenticationPrincipal User user, Model model) {
        Pageable pageable = PageRequest.of(0, pageSize);
        model.addAttribute("orders",
            orderRepo.findByUserOrderByPlacedAtDesc(user, pageable));
        return "orderList";
    }
}
```
{% endcode %}

程序清单 5.1 中最重要的变化是增加了 @ConfigurationProperties 注解。其 prefix 属性设置为 taco。这意味着在设置 pageSize 属性时，需要使用一个名为 taco.orders.pageSize 的配置属性。

新的 pageSize 属性默认为 20。但是可以通过设置 taco.orders.pageSize 属性轻松地将其更改为想要的任何值。例如，可以在 application.yml 中设置此属性：

```yaml
taco:
  orders:
    pageSize: 10
```

或者，如果需要在生产环境中进行快速更改，可以通过设置 taco.orders.pageSize 属性作为环境变量来重新构建和重新部署应用程序：

```bash
$ export TACO_ORDERS_PAGESIZE=10
```

可以设置配置属性的任何方法，都可以用来调整最近订单页面的大小。接下来，我们将研究如何在属性持有者中设置配置数据。

