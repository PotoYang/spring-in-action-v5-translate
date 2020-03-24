# 6.2 启用超媒体

到目前为止，创建的 API 是相当基本的，但是只要使用它的客户机知道 API 的 URL 模式，它就可以工作。例如，客户端可能被硬编码，知道它可以向 `/design/recent` 接口发出 GET 请求来获得最近创建的 tacos 列表。同样地，它可能是硬编码的，以知道它可以将该列表中的任何 taco 的 ID 附加到 `/design` 接口中，以获得特定 taco 资源的 URL。

使用硬编码的 URL 模式和字符串操作在 API 客户机代码中很常见。但是请想象一下，如果 API 的 URL 模式改变了，会发生什么。硬编码的客户端代码相对于 API 已经过时了，因此会被破坏。硬编码 API url 并在其上使用字符串操作会使客户端代码变得兼容性弱。

_超媒体作为应用程序状态的引擎_（HATEOAS），是一种创建自描述 API 的方法，其中从 API 返回的资源包含到相关资源的链接。这使客户机能够在对 API 的 url 了解最少的情况下引导 API。相反，它理解 API 提供的资源之间的关系，并在遍历这些关系时使用对这些关系的理解来发现 API 的 url。

例如，假设一个客户端请求一个最近设计的 tacos 列表。在它的原始形式，没有超链接，最近的 tacos 列表将在客户端以 JSON 的形式接收，看起来像这样（为了简洁起见，除了列表中的第一个taco 外，其他都被剪掉了）：

```text
[
    {
        "id": 4,
        "name": "Veg-Out",
        "createdAt": "2018-01-31T20:15:53.219+0000",
        "ingredients": [
            {"id": "FLTO", "name": "Flour Tortilla", "type": "WRAP"},
            {"id": "COTO", "name": "Corn Tortilla", "type": "WRAP"},
            {"id": "TMTO", "name": "Diced Tomatoes", "type": "VEGGIES"},
            {"id": "LETC", "name": "Lettuce", "type": "VEGGIES"},
            {"id": "SLSA", "name": "Salsa", "type": "SAUCE"}
        ]
    },
    ...
]
```

如果客户端希望在 taco 本身上获取或执行其他 HTTP 操作，则需要（通过硬编码）知道可以将 id 属性的值附加到路径为 `/design` 的 URL。同样，如果它希望对其中一个成分执行 HTTP 操作，它需要知道它可以将成分的 id 附加到路径为 `/ingredients` 的 URL。在这两种情况下，都需要在路径前面加上 `http://` 或 `https://` 和 API 的主机名。

相反，如果使用超媒体启用了 API，则该 API 将描述自己的 url，从而使客户端无需进行硬编码。如果嵌入了超链接，那么最近创建的 tacos 列表可能与下面的列表类似。

{% code title="程序清单 6.3 包含超链接的 taco 资源列表" %}
```text
{
    "_embedded": {
        "tacoResourceList": [
            {
                "name": "Veg-Out",
                "createdAt": "2018-01-31T20:15:53.219+0000",
                "ingredients": [
                    {
                        "name": "Flour Tortilla", "type": "WRAP",
                        "_links": {
                            "self": { "href": "http://localhost:8080/ingredients/FLTO" }
                        }
                    },
                    {
                        "name": "Corn Tortilla", "type": "WRAP",
                        "_links": {
                            "self": { "href": "http://localhost:8080/ingredients/COTO" }
                        }
                    },
                    {
                        "name": "Diced Tomatoes", "type": "VEGGIES",
                        "_links": {
                            "self": { "href": "http://localhost:8080/ingredients/TMTO" }
                        }
                    },
                    {
                        "name": "Lettuce", "type": "VEGGIES",
                        "_links": {
                            "self": { "href": "http://localhost:8080/ingredients/LETC" }
                        }
                    },
                    {
                        "name": "Salsa", "type": "SAUCE",
                        "_links": {
                            "self": { "href": "http://localhost:8080/ingredients/SLSA" }
                        }
                    }
                ],
                "_links": {
                    "self": { "href": "http://localhost:8080/design/4" }
                }
            },
            ...
        ]
    },
    "_links": {
        "recents": {
            "href": "http://localhost:8080/design/recent"
        }
    }
}
```
{% endcode %}

这种特殊风格的 HATEOAS 被称为 HAL（超文本应用语言）这是一种简单且常用的格式，用于在 JSON 响应中嵌入超链接。

虽然这个列表不像以前那样简洁，但它确实提供了一些有用的信息。这个新的 tacos 列表中的每个元素都包含一个名为 \_links 的属性，该属性包含用于客户端引导的 API 超链接。在本例中，tacos 和 ingredients 都有引用这些资源的自链接，整个列表都有一个引用自身的 recents 链接。

如果客户端应用程序需要对列表中的 taco 执行 HTTP 请求，则不需要了解 taco 资源的 URL 是什么样子的。相反，它知道请求自链接，该链接映射到 [http://localhost:8080/design/4](http://localhost:8080/design/4)。如果客户想要处理特定的成分，它只需要遵循该成分的自链接。

Spring HATEOAS 项目为 Spring 提供了超链接支持。它提供了一组类和资源汇编器，可用于在从 Spring MVC 控制器返回资源之前向资源添加链接。

要在 Taco Cloud API 中启用超媒体，需要将 Spring HATEOAS starter 依赖项添加到构建中：

```markup
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-hateoas</artifactId>
</dependency>
```

这个启动程序不仅将 Spring HATEOAS 添加到项目的类路径中，还提供了自动配置来启用 Spring HATEOAS。需要做的就是重新设计控制器以返回资源类型，而不是域类型。首先，将超级媒体链接添加到 `/design/recent` 的 GET 请求中，用于返回的最近的 tacos 列表。

