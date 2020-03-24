# 5.1 微调自动配置

在我们深入研究配置属性之前，有必要确定在 Spring 中有两种不同（但相关）的配置

* _Bean wiring_ —— 它声明应用程序组件将在 Spring 应用程序上下文中作为 bean 创建，以及它们应该如何相互注入。
* _Property injection_ —— 在 Spring 应用程序上下文中设置 bean 的值。

在 Spring 的 XML 和基于 Java 的配置中，这两种类型的配置通常在同一个地方显式地声明。在 Java 配置中，@Bean 注解的方法可能实例化一个 bean，然后设置其属性的值。例如，考虑下面的 @Bean 方法，它为嵌入式 H2 数据库声明了一个数据源：

```text
@Bean
public DataSource dataSource() {
    return new EmbeddedDataSourceBuilder()
        .setType(H2)
        .addScript("taco_schema.sql")
        .addScripts("user_data.sql", "ingredient_data.sql")
        .build();
}
```

这里的 addScript\(\) 和 addScripts\(\) 方法设置了一些带有 SQL 脚本名称的字符串属性，这些 SQL 脚本应该在数据源准备好后应用到数据库中。如果不使用 Spring Boot，那么这就是配置 DataSource bean 的方式，而自动配置使此方法完全没有必要。

如果 H2 依赖项在运行时类路径中可用，那么 Spring Boot 将在 Spring 应用程序上下文中自动创建适当的数据源 bean。bean 应用于 schema.sql 和 data.sql 脚本的读取。

但是，如果希望将 SQL 脚本命名为其他名称呢？或者，如果需要指定两个以上的 SQL 脚本怎么办？这就是配置属性的用武之地。但是在开始使用配置属性之前，需要了解这些属性的来源。

