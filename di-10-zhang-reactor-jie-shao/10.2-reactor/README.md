# 10.2 Reactor

响应式编程需要我们从与命令式编程完全不同的角度去思考。响应式编程是通过直接建立一个用于数据流通的管道，而不是描述一系列需要进行的步骤。作为数据流通的管道，它可以被改变或以某种方式被使用。

例如，假设你想到利用一个人的名字，把它的所有字母变为大写，然后用它来创建一个问候语，最后将它打印出来。在命令式编程模型中，代码会是这个样子：

```java
String name = "Craig";
String capitalName = name.toUpperCase();
String greeting = "Hello, " + capitalName + "!";
System.out.println(greeting);
```

在命令式编程中，每一行为一步，一步接着一步，同时绝对是在同一个线程中。每一步都会阻塞直到完成才能进行下一步动作。

相反，函数式的响应式代码可以以下面这种方式达到目的：

```java
Mono.just("Craig")
    .map(n -> n.toUpperCase())
    .map(cn -> "Hello, " + cn + "!")
    .subscribe(System.out::println);
```

不要太担心这个例子中的细节；我们很快将讨论所有关于 just\(\)、map\(\) 和 subscribe\(\) 的操作。就目前而言，要了解的是，虽然响应式的例子似乎仍然遵循一步一步的模式，但是这确实是一个用于数据流的管道。在管道的每个阶段，数据被以某种方式修改了，但是不能知道哪一步操作被哪一个线程执行了的。它们可能在同一个线程也可能不是。

例子中的 Mono 是 Reactor 的两个核心类型之一，另一个是 Flux。两者都是响应式流的 Publisher 的实现。Flux 表示零个、一个或多个（可能是无限个）数据项的管道。Mono 特定用于已知的数据返回项不多于一个的响应式类型。

> Reactor 与 RxJava
>
> 如果你已经熟悉 RxJava 或 ReactiveX，你可能会认为 Mono 和 Flux 听起来很像 Observable 和 Single。事实上，它们在语义上近似相等，甚至提供许多相同的操作。
>
> 尽管我们在本书中重点讨论 Reactor，但可以在 Reactor 和 RxJava 类型之间进行转换。此外，你将在下面的章节中看到 Spring 还可以使用 RxJava 的类型。

在前面的例子中实际上有三个 Mono。just\(\) 操作创建了第一个。当 Mono 发出一个值时，该值将被赋予大写操作的 map\(\) 并用于创建另一个 Mono。当第二个 Mono 发布其数据时，被赋予第二个 map\(\) 操作来执行一些字符串连接，其结果用于创建第三个 Mono。最后，对 Mono 的 subscribe\(\) 进行调用，接收数据并打印它。

