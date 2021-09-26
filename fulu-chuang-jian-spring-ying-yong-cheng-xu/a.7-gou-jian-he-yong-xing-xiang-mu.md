# A.7 构建和运行项目
无论您如何创建项目，都可以使用 java -jar 命令的运行它：

```bash
% java -jar demo.jar
```

即使您构建的是 WAR 文件而不是 JAR 文件，这仍然可行：

```bash
% java -jar demo.war
```

您还可以利用 Spring Boot Maven 和 Gradle 插件来运行您的应用程序。如果您的项目是使用 Maven 构建的，则可以用如下方式运行：

```bash
% mvn spring-boot:run
```

另一方面，如果您使用 Gradle 构建项目，则可以按如下所示运行您的项目：

```bash
% gradle bootRun
```

无论是使用 Maven 还是 Gradle，构建工具都会首先构建您的项目（如果尚未构建），然后运行它。