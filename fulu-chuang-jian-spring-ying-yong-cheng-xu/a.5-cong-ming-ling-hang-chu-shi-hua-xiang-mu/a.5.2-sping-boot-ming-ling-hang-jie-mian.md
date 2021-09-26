# A.5.2 Spring Boot 命令行界面
Spring Boot CLI 是创建 Spring 应用程序的另一个选择。您可以用多种方式安装 Spring Boot CLI，最简单的方式（也是我最喜欢的方式）就是使用 SDKMAN ([http://sdkman.io/](http://sdkman.io/)):

```bash
% sdk install springboot
```

安装 Spring Boot CLI 后，您就可以用它来创建项目，和使用 curl 的方式类似。您将使用的命令是 spring init。事实上，以最简单的方式使用 Spring Boot CLI 生成项目，只需使用如下命令：

```bash
% spring init
```

这会生成一个最简单的 Spring Boot 项目，并下载名为 demo.zip 的压缩文件。

然而，您可能需要指定更多细节以及依赖项。表 A.2 列出了 spring init 命令可用的所有参数。

表 A.2 spring init 命令支持的请求参数

| 参数 | 描述 | 默认值 |
| :--- | :--- | :--- |
| group-id | 项目的 group ID，指在 Maven 存储库中的组织。 | com.example |
| artifact-id | 项目的 artifact ID，会出现在 Maven 存储库中。 | demo |
| version | 项目版本。 | 0.0.1-SNAPSHOT |
| name | 项目名称，同时也用于应用程序主类名称（带有 Application 后缀）。 | demo |
| description | 项目描述。 | Spring Boot 工程 demo |
| package-name | 项目基类名称。 | com.example.demo |
| dependencies | 项目中的依赖项。 | The base Spring Boot starter |
| type | 项目类型，maven-project 或 gradle-project。 | maven-project |
| java-version | 项目构建使用的 Java 版本。 | 1.8 |
| boot-version | 项目构建使用的 Spring Boot 版本。 | Spring Boot 正式发布版本 |
| language | 项目代码使用的编程语言： java、groovy、kotlin。 | java |
| packaging | 项目构建产物类型：jar、war。 | jar |

使用 --list 参数，您可以获得此参数列表以及可用依赖项列表：

```bash
% spring init --list
```

假设您希望创建一个基于 Java 1.7 的 web 应用程序，可以使用 --dependencies 和 --java 参数来实现这些功能：

```bash
% spring init --dependencies=web --java-version=1.7
```

或者假设您想要创建一个 web 应用程序，其中要用 Spring Data JPA 进行持久化，您希望使用 Gradle 而不是 Maven 来执行构建。您可以使用以下命令

```bash
% spring init --dependencies=web,jpa --type=gradle-project
```

您可能还注意到，许多 spring init 参数与 curl 命令行的参数相同或相似。区别是：spring init 命令有一些参数没有支持（例如： baseDir），并且参数名以中划线分隔，而不是用驼峰方式（例如，package-name 与 packageName）。
