# A.5 从命令行初始化项目

基于 IDE 和浏览器界面的 Spring Initializr，是最常见的创建 Spring 项目的方式。他们都只是 Initializr 应用 REST 服务的客户端。在某些特殊情况下（例如，脚本场景中），您可能会发现直接从命令行使用 Initializr 服务很有用。

使用 API 有两种方法：
* 使用 curl 命令（或类似的命令行 REST 客户端命令）
* 使用 Spring Boot 命令行界面（又名 Spring Boot CLI）

让我们分别讨论这两种方法，从 curl 命令开始。