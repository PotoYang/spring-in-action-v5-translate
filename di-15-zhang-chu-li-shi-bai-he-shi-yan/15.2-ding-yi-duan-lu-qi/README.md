# 15.2 定义断路器

在声明断路器之前，您需要为服务添加 Spring Cloud Netflix Hystrix 的依赖。在 Maven pom.xml 中的依赖项如下所示：

```markup
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

作为 Spring Cloud 组件的一部分，您还需要声明依赖管理。在我写这篇文章的时候，最新的发布版本为 Finchley.SR1。因此，Spring Cloud 的版本应该设置一下，以下条目应出现在 pom.xml 文件的 `<dependency-Management>` 部分：

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

> 注意，创建项目时在 Initializr 中也可以通过勾选 Hystrix 复选框来添加依赖。如果使用 Initializr 将 Hystrix 添加到项目中，则依赖管理部分会自动为您创建好。

有了 Hystrix 依赖，接下来需要做的就是启用 Hystrix。这通过对每个应用程序的主配置添加 `@EnableHystrix` 注解来实现。例如，要在 ingredient 服务中启用 Hystrix，您可以这样注解 IngredientServiceApplication 类：

```java
@SpringBootApplication
@EnableHystrix
public class IngredientServiceApplication {
  ...
}
```

此时，应用程序中启用了 Hystrix，但这只意味着可以声明断路器了。您还没有在任何一个方法上声明使用断路器。这就要通过使用 `@HystrixCommand` 注解来实现。

任何用 `@HystrixCommand` 注解的方法都将被声明为具有断路器切面。例如，考虑下面这个方法，使用负载平衡的 RestTemplate 从 ingredient 服务查询 Ingredient 实体：

```java
public Iterable<Ingredient> getAllIngredients() {
  ParameterizedTypeReference<List<Ingredient>> stringList =
    new ParameterizedTypeReference<List<Ingredient>>() {};
  return rest.exchange(
    "http://ingredient-service/ingredients", HttpMethod.GET,
    HttpEntity.EMPTY, stringList).getBody();
}
```

对 `exchange()` 的调用可能会引起问题。如果没有在 Eureka 中注册 `ingredient-service`，或者由于一些原因请求失败了，那么将抛出 `RestClientException` 异常（未检查的异常）。因为异常没有使用 `try/catch` 处理，调用方必须处理这个异常。如果调用方不处理，那么它将继续向上抛出到调用堆栈的上游。如果上游也没有处理，错误就会级联抛出到任何上游微服务或客户端。

未捕获的异常在任何应用程序中都是一个挑战，在微服务中尤其如此。当涉及到失败时，微服务应该遵循维加斯规则——发生在微服务中的，只停留在微服务中。在 `getAllIngredients()` 方法上声明断路器以满足该规则。

您只需要对方法添加 `@HystrixCommand` 注解，然后提供一个降级方法。首先，让我们将 `@HystrixCommand` 添加到 `getAllIngredients()` 方法上：

```java
@HystrixCommand(fallbackMethod="getDefaultIngredients")
public Iterable<Ingredient> getAllIngredients() {
  ...
}
```

有一个断路器保护它不发生故障，现在 `getAllIngredients()` 方法是故障安全的。无论出于何种原因，从 `getAllIngredients()` 中抛出未捕获的异常，断路器都将捕获它们，并将方法调用重定向到 `getDefaultIngredients()` 方法。

降级方法可以做任何您想让他们做的事情，但是目的是，在最初使用的方法无法使用时，提供备份行为。降级方法的唯一规则是它要具有与原方法相同的签名（除了方法名）。

要满足此要求， `getAllIngredients()` 方法必须也没有请求参数，并返回 `List<Ingredient>`。`getAllIngredients()` 满足该规则并返回默认列表数据：

```java
private Iterable<Ingredient> getDefaultIngredients() {
  List<Ingredient> ingredients = new ArrayList<>();
  ingredients.add(new Ingredient(
        "FLTO", "Flour Tortilla", Ingredient.Type.WRAP));
  ingredients.add(new Ingredient(
        "GRBF", "Ground Beef", Ingredient.Type.PROTEIN));
  ingredients.add(new Ingredient(
        "CHED", "Shredded Cheddar", Ingredient.Type.CHEESE));
return ingredients;
}
```

现在，如果 `getAllIngredients()` 方法失败了，断路器将调用 `getDefaultIngredients()`，调用者将收到一个默认列表。

您可能想知道降级方法本身是否有断路器。尽管上面的 `getDefaultIngredients()` 实现不会出什么问题，但有可能其他实现会有潜在的失败点。在这种情况下，您可以为 `getDefaultIngredients()` 添加 `@HystrixCommand`，并提供另一个降级方法。事实上，如果需要，您可以堆叠尽可能多的降级方法。唯一的限制是在降级的底部，必须有一个方法不发生故障且不再需要断路器的堆栈。

