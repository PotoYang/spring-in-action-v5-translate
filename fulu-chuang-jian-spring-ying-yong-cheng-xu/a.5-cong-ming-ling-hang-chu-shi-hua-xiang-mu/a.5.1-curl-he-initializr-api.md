# A.5.1 curl 和 Initializr API

使用 curl 创建 Spring 项目的最简单方法是使用如下 API：

```bash
% curl https://start.spring.io/starter.zip -o demo.zip
```

在本例中，您请求 /starter.zip 端点， Initializr 将生成一个 Spring 项目，并将工程打包为 zip 文件返回。生成的项目
是使用 Maven 构建的，除了基本 Spring Boot starter 之外，没有其他依赖项。pom.xml 文件中的所有项目信息都设置了默认值。

如果没有特殊指定，下载的文件名为 starter.zip。在本例中，使用 -o 选项指定了下载的文件为 demo.zip。

公共 Spring Initializr 服务位于 [https://start.spring.io](https://start.spring.io)，如果您使用的是自定义 Initializr，则需要相应地调整 URL 地址。

您可能需要设置一些其他细节，而不是全部使用默认值。表 A.1 列出了 Spring Initializr REST 服务的所有参数（及其默认值）。


**表 A.1：Initializer API 支持的请求参数**

| 参数 | 描述 | 默认值 |
| :--- | :--- | :--- |
| groupId | 项目的 group ID，指在 Maven 存储库中的组织。 | com.example |
| artifactId | 项目的 artifact ID，会出现在 Maven 存储库中。 | demo |
| version | 项目版本。 | 0.0.1-SNAPSHOT |
| name | 项目名称，同时也用于应用程序主类名称（带有 Application 后缀）。 | demo |
| description | 项目描述。 | Spring Boot 工程 demo |
| packageName | 项目基类名称。 | com.example.demo |
| dependencies | 项目中的依赖项。 | The base Spring Boot starter |
| type | 项目类型，maven-project 或 gradle-project。 | maven-project |
| javaVersion | 项目构建使用的 Java 版本。 | 1.8 |
| bootVersion | 项目构建使用的 Spring Boot 版本。 | Spring Boot 正式发布版本 |
| language | 项目代码使用的编程语言： java、groovy、kotlin。 | java |
| packaging | 项目构建产物类型：jar、war。 | jar |
| applicationName | 应用名称。 | name 参数的值 |
| baseDir | 构建时基础路径。 | 根目录 |

通过向 Initializr URL 发送请求，您可以获得此参数列表以及可用依赖项列表：

```bash
% curl https://start.spring.io
```

您可能发现，dependencies 参数可能是最有用的。例如，假设您希望使用 Spring 创建一个简单的 web 项目。参考以下的 curl 命令，这将生成一个包含 web starter 依赖的项目 zip 包：

```bash
% curl https://start.spring.io/starter.zip \
      -d dependencies=web \
      -o demo.zip
```

举一个更复杂些的示例，假设您想要开发一个应用，要使用 Spring Data JPA进行数据持久化，用 Gradle 进行构建，而且项目应该位于 my-dir 目录下。并且您不只是下载一个 zip 文件，而是希望项目下载时自动解压，并导入文件系统。在这种情况下，应该使用以下命令：

```bash
% curl https://start.spring.io/starter.tgz \
      -d dependencies=web,data-jpa \
      -d type=gradle-project
      -d baseDir=my-dir | tar -xzvf -
```

这样，下载的 zip 文件会通过管道命令传输到 tar 命令进行解压。