# 4.3 保护 web 请求

Taco Cloud 的安全需求应该要求用户在设计 tacos 或下订单之前进行身份验证。但是主页、登录页面和注册页面应该对未经身份验证的用户可用。

要配置这些安全规则，需要介绍一下 WebSecurityConfigurerAdapter 的另一个 configure\(\) 方法：

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    ...
}
```

这个 configure\(\) 方法接受 HttpSecurity 对象，可以使用该对象来配置如何在 web 级别处理安全性。可以配置 HttpSecurity 的属性包括：

* 在允许服务请求之前，需要满足特定的安全条件
* 配置自定义登录页面
* 使用户能够退出应用程序
* 配置跨站请求伪造保护

拦截请求以确保用户拥有适当的权限是配置 HttpSecurity 要做的最常见的事情之一。让我们确保 Taco Cloud 的客户满足这些要求。

