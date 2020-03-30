# 11.3 测试响应式 Controller

在测试响应式 Controller 时，Spring 5 并没有让我们陷入困境。实际上，Spring 5 引入了 WebTestClient，这是一个新的测试程序，它让用 Spring WebFlux 编写的响应式 Controller 变得容易测试。让我们首先使用它测试在第 11.1.2 节中编写的 DesignTacoController 中的 recentTacos\(\) 方法，来了解如何使用 WebTestClient 编写测试用例。

