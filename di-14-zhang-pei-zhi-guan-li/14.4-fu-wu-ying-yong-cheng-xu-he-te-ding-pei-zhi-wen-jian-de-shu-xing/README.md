# 14.4 服务应用程序和特定配置文件的属性

回忆一下，当使用 Config Server 服务的客户端在启动时，它会向 Config Server 发出请求。请求路径中包含应用程序名称和当前 profile 名称。Config Server 在提供配置数据时，依据这些值将特定于应用程序和 profile 的配置返回给客户端。

从客户端的角度来看，使用特定于应用程序名称和特定于 profile 的配置，与不使用 Config Server 时没有太大区别。应用程序名称是通过设置 `spring.application.name` 属性来指定的（同样也是在 Eureka 中注册时用的名称）。并且可以通过设置 `spring.profiles.active` 属性指定当前激活的 profile（通常在环境变量 `SPRING_PROFILES_ACTIVE` 指定）。

同样，为特定应用程序或 profile 提供配置服务时，Config Server 本身中也不需要做太多的工作。其实最重要的是，这些属性是如何在 Git 中存储的。

