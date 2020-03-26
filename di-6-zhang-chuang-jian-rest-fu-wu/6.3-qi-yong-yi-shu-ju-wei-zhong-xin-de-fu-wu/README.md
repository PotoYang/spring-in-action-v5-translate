# 6.3 启用以数据为中心的服务

正如在第 3 章中看到的，Spring Data 拥有一种特殊的魔力，它根据在代码中定义的接口自动创建存储库的实现。但是 Spring Data 还有另一个技巧，可以为应用程序定义 API。

Spring Data REST 是 Spring Data 家族中的另一个成员，它为 Spring Data 创建的存储库自动创建REST API。只需将 Spring Data REST 添加到构建中，就可以获得一个 API，其中包含所定义的每个存储库接口的操作。

要开始使用 Spring Data REST，需要在构建中添加以下依赖项：

```markup
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-rest</artifactId>
</dependency>
```

信不信由你，这就是在一个已经将 Spring Data 用于自动存储库的项目中公开 REST API 所需要的全部内容。通过在构建中简单地使用 Spring Data REST starter，应用程序可以自动配置，从而为 Spring Data 创建的任何存储库（包括 Spring Data JPA、Spring Data Mongo 等）自动创建 REST API。

Spring Data REST 创建的 REST 端点至少与自己创建的端点一样好（甚至可能更好）。因此，在这一点上，可以做一些拆卸工作，并在继续之前删除到目前为止创建的任何 @RestController 注解的类。

要尝试 Spring Data REST 提供的端点，可以启动应用程序并开始查看一些 url。基于已经为 Taco Cloud 定义的存储库集，应该能够执行针对 Taco、Ingredient、Order 和 User 的 GET 请求。

例如，可以通过向 `/ingredients` 接口发出 GET 请求来获得所有 Ingredient 的列表。使用 curl，可能会得到这样的结果（经过删节，只显示第一个 Ingredient）：

```bash
$ curl localhost:8080/ingredients
{
    "_embedded" : {
        "ingredients" : [
            {
                "name" : "Flour Tortilla",
                "type" : "WRAP",
                "_links" : {
                    "self" : {"href" : "http://localhost:8080/ingredients/FLTO"},
                    "ingredient" : {
                        "href" : "http://localhost:8080/ingredients/FLTO"
                    }
                }
            },
            ...
        ]
    },
    "_links" : {
        "self" : {
            "href" : "http://localhost:8080/ingredients"
        },
        "profile" : {
            "href" : "http://localhost:8080/profile/ingredients"
        }
    }
}
```

哇！通过向构建中添加一个依赖项，不仅获得了 Ingredient 的端点，而且返回的资源也包含超链接！假装是这个 API 的客户端，也可以使用 curl 来跟踪特定入口的自链接：

```bash
$ curl localhost:8080/ingredients/FLTO
{
    "name" : "Flour Tortilla",
    "type" : "WRAP",
    "_links" : {
        "self" : {
            "href" : "http://localhost:8080/ingredients/FLTO"
        },
        "ingredient" : {
            "href" : "http://localhost:8080/ingredients/FLTO"
        }
    }
}
```

为了避免过于分散注意力，在本书中我们不会浪费太多时间来深入研究 Spring Data REST 创建的每个端点和选项。但是应该知道，它还支持其创建的端点的 POST、PUT 和 DELETE 方法。没错：可以通过向 `/ingredients` 接口发送 POST 请求创建一个新的 Ingredient，然后通过向 `/indegredient/FLTO` 接口发送 DELETE 请求来从菜单上移除面粉玉米饼。

可能想要做的一件事是为 API 设置一个基本路径，这样它的端点是不同的，并且不会与编写的任何控制器发生冲突。（事实上，如果不删除先前创建的 IngredientController，它将干扰 Spring Data REST 提供的 `/ingredients` 端点。）要调整 API 的基本路径，请设置 spring.data.rest 基本路径属性：

```yaml
spring:
  data:
    rest:
      base-path: /api
```

这将设置 Spring Data REST 端点的基本路径为 `/api`。因此，Ingredient 端点现在是 `/api/ingredients`。现在，通过请求一个 tacos 列表来使用这个新的基本路径：

```bash
$ curl http://localhost:8080/api/tacos
{
    "timestamp": "2018-02-11T16:22:12.381+0000",
    "status": 404,
    "error": "Not Found",
    "message": "No message available",
    "path": "/api/tacos"
}
```

噢？这并没有达到预期的效果。有一个 Ingredient 实体和一个 IngredintRepository 接口，其中 Spring Data REST 暴露 `/api/ingredients` 端点。因此，如果有一个 Taco 实体和一个 TacoRepository 接口，为什么 Spring Data REST 不能提供 `/api/tacos` 端点呢？

