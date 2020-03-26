# 5.3 使用 profile 文件进行配置

当应用程序部署到不同的运行时环境时，通常会有一些配置细节不同。例如，数据库连接的细节在开发环境中可能与在 QA 环境中不一样，在生产环境中可能还不一样。在一个环境中唯一配置属性的一种方法是使用环境变量来指定配置属性，而不是在 application.properties 或 application.yml 中定义它们。

例如，在开发期间，可以依赖于自动配置的嵌入式 H2 数据库。但在生产中，可以将数据库配置属性设置为环境变量，如下所示：

```bash
% export SPRING_DATASOURCE_URL=jdbc:mysql://localhost/tacocloud
% export SPRING_DATASOURCE_USERNAME=tacouser
% export SPRING_DATASOURCE_PASSWORD=tacopassword
```

尽管这样做是可行的，但是将一两个以上的配置属性指定为环境变量就会变得有点麻烦。此外，没有跟踪环境变量更改的好方法，也没有在出现错误时轻松回滚更改的好方法。

相反，我更喜欢利用 Spring profile 文件。profile 文件是一种条件配置类型，其中根据运行时激活的 profile 文件应用或忽略不同的 bean、配置类和配置属性。

例如，假设出于开发和调试的目的，希望使用嵌入式 H2 数据库，并且希望将 Taco Cloud 代码的日志级别设置为 DEBUG。但是在生产中，需要使用一个外部 MySQL 数据库，并将日志记录级别设置为 WARN。在开发环境中，很容易不设置任何数据源属性并获得自动配置的 H2 数据库。至于 DEBUG 级别的日志记录，可以在 application.yml 中设置 logging.level.tacos 属性。

```yaml
logging:
  level:
    tacos: DEBUG
```

这正是开发目的所需要的。但是，如果要将此应用程序部署到生产环境中，而不需要对 application.yml 进行进一步更改，仍然可以获得对于 tacos 包的调试日志和嵌入式 H2 数据库。需要的是定义一个具有适合生产的属性的 profile 文件。

