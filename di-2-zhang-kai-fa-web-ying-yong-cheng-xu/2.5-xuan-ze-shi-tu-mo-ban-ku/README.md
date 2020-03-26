# 2.5 选择视图模板库

### 2.5 选择视图模板库

在大多数情况下，对视图模板库的选择取决于个人喜好。Spring 非常灵活，支持许多常见的模板选项。除了一些小的例外，所选择的模板库本身甚至不知道它是在 Spring 中工作的。

表 2.2 列出了 Spring Boot 自动配置支持的模板选项。

**表 2.2 支持的模板选项**

| 模板 | Spring Boot starter 依赖 |
| :--- | :--- |
| FreeMarker | spring-boot-starter-freemarker |
| Groovy Templates | spring-boot-starter-groovy-templates |
| JavaServer Page\(JSP\) | None \(provided by Tomcat or Jetty\) |
| Mustache | spring-boot-starter-mustache |
| Thymeleaf | spring-boot-starter-thymeleaf |

一般来说，可以选择想要的视图模板库，将其作为依赖项添加到构建中，然后开始在 /templates 目录中（在 Maven 或 Gradl 构建项目的 src/main/resources 目录下）编写模板。Spring Boot 将检测选择的模板库，并自动配置所需的组件来为 Spring MVC 控制器提供视图。

已经在 Taco Cloud 应用程序中用 Thymeleaf 实现了这一点。在第 1 章中，在初始化项目时选择了 Thymeleaf 复选框。这导致 Spring Boot 的 Thymeleaf starter 被包含在 pom.xml 文件中。当应用程序启动时，Spring Boot 自动配置会检测到 Thymeleaf 的存在，并自动配置 Thymeleaf bean。现在要做的就是开始在 /templates 中编写模板。

如果希望使用不同的模板库，只需在项目初始化时选择它，或者编辑现有的项目构建以包含新选择的模板库。

例如，假设想使用 Mustache 而不是 Thymeleaf。没有问题。只需访问项目 pom.xml 文件，将：

```markup
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

替换为：

```markup
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mustache</artifactId>
</dependency>
```

当然，需要确保使用 Mustache 语法而不是 Thymeleaf 标签来编写所有模板。Mustache 的使用细节（或选择的任何模板语言）不在这本书的范围之内，但为了让你知道会发生什么，这里有一个从 Mustache 模板摘录过来的片段，这个片段渲染了玉米饼设计表单的成分列表中的一个：

```markup
<h3>Designate your wrap:</h3>
{{#wrap}}
<div>
    <input name="ingredients" type="checkbox" value="{{id}}" />
    <span>{{name}}</span><br/>
</div>
{{/wrap}}
```

这是 Mustache 与第 2.1.3 节中的 Thymeleaf 片段的等价替换。`{{#wrap}}` 块（以 `{{/wrap}}` 结尾）迭代 request 属性中的一个集合，该集合的键为 wrap，并为每个项目呈现嵌入的 HTML。`{{id}}` 和 `{{name}}` 标记引用项目的 id 和 name 属性（应该是一个 Ingredient）。

在表 2.2 中请注意，JSP 在构建中不需要任何特殊的依赖关系。这是因为 servlet 容器本身（默认情况下是 Tomcat）实现了 JSP 规范，因此不需要进一步的依赖关系。

但是如果选择使用 JSP，就会遇到一个问题。事实证明，Java servlet 容器 —— 包括嵌入式 Tomcat 和 Jetty 容器 —— 通常在 /WEB-INF 下寻找 jsp。但是如果将应用程序构建为一个可执行的 JAR 文件，就没有办法满足这个需求。因此，如果将应用程序构建为 WAR 文件并将其部署在传统的 servlet 容器中，那么 JSP 只是一个选项。如果正在构建一个可执行的 JAR 文件，必须选择 Thymeleaf、FreeMarker 或表 2.2 中的其他选项之一。

