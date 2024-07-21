# Java

[廖雪峰 Java 教程](https://www.liaoxuefeng.com/wiki/1252599548343744/1255883772263712)

## 下载与安装

![20210630131230](http://image.zuoright.com/20210630131230.png)

- JRE, Java Runtime Environment
- JDK, [Java Development Kit](https://www.oracle.com/java/technologies/javase-downloads.html)
    - 有些软件在高版本 JDK 环境下会有各种莫名其妙的问题，可以使用 [Java 8](https://www.oracle.com/java/technologies/downloads/?er=221886#java8)

- Mac

```shell
# Java
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-16.0.1.jdk/Contents/Home  # Library: 资源库
export PATH=$PATH:$JAVA_HOME/bin
```

- Windows

```shell
# JAVA_HOME
C:\Program Files\Java\jdk-11.0.12

# Path
%JAVA_HOME%\bin;
%JAVA_HOME%\jre\bin;
```

## demo

Java 是一种面向对象的编程语言，所有的代码必须在类的上下文中定义和执行。

```java
// 定义一个公开的类，一个文件只能有一个public类型的class
// 类名为Hello
public class Hello {
    // 定义一个公开的静态方法，void为方法的返回类型
    // 方法名为main，Java规定程序从固定的main方法开始执行
    // 参数名为args，参数类型为String[]
    public static void main(String[] args) {
        // 打印一个字符串到屏幕上
        System.out.println("Hello, world!");  // 代码行以分号结尾
    }
}
```

必需保存为与类名相同的文件名：Hello.java

运行

```shell
javac Hello.java  # 先编译，生成demo.class
java Hello  # 参数Hello为类名，JVM会自动查找对应的.class并运行
```

## 构建工具

Java 程序的构建过程一般是：编译、测试、打包。

- javac 编译器，把 `.java` 源码编译为 `.class` 字节码
- jdb 调试器
- jar 把一组 `.class` 文件打包成 `.jar` 包
- java 可执行程序，其实就是 JVM

```shell
java -version
javac -version
```

如果文件比较少，我们可以使用 javac, java, jar 这些命令手动构建

但当工程越来越大，文件越来越多，这些机械重复的工作就需要交给工具来帮我们完成了：

- ant 与 makefile 比较像，定义任务，规定依赖，执行任务，缺点是没办法管理依赖
- [Maven](https://www.liaoxuefeng.com/wiki/1252599548343744/1255945359327200) 提出仓库的概念，缺点是使用 xml 语法不简洁，无法自定义任务
- gradle 继承 maven 和 ant 的优点，可以用仓库管理依赖也能自定义任务，`build.gradle` 基于脚本语言 Groovy

## Servlet

Java Web 相关的标准都是在 EE 中定义的，Servlet(Server Applet) API 是一个 jar 包，通过构建工具引入它，与其他一些文件按固定结构组织并打包为 `.war` 文件

> `war`(Java Web Application Archive) 构建后的 Web 应用程序

普通的 Java 程序是通过启动 JVM，然后执行 `main()` 开始运行。

但是 Web 应用程序，需要启动应用服务器（也称为 Servlet 容器）加载 war 包来运行 Servlet

常用的 Servlet 容器有

- `Jetty`：由 Eclipse 开发的开源免费服务器
- `Tomcat`：由 Apache 开发的开源免费服务器
- `GlassFish`：一个开源的全功能 JavaEE 服务器

Servlet 运行在 Tomcat 这样的容器中，一行行拼接生成 HTML，效率太低，于是有了 JSP, Java Server Pages 动态网页技术

JSP 是为了解决 Servelet 开发效率低下不方便开发而生的，本质还是 Servlet，把拼 HTML 自动化了，是对 Servlet 的一种补充

## JavaEE

![20230925100617](https://image.zuoright.com/20230925100617.png)

JavaEE 是企业版，它只是在 JavaSE 的基础上加上了大量的 API 和库，以便方便开发 Web 应用、数据库、消息服务等

- Servlet + JSP + JavaBean，容器：Tomcat
- Spring MVC，容器：IoC
- Spring Boot：开箱即用

## Spring Cloud

参考：<https://juejin.cn/post/6844904101055037448>

![20240625215700](https://image.zuoright.com/20240625215700.png)

Spring Cloud 包含很多子项目，第一代主要以 Netflix 的开源组件为主，包括 Eureka、Ribbon、Feign、Hystrix、Zuul、Archaius 等，其中 Eureka，Hystrix 等不再维护，但目前不影响使用。

第二代 主要以 Alibaba 生态组件为主

## JConsole

命令行输入 `jconsole` 启动控制台，可以看到运行了哪些 Java 进程

![20230823121213](https://image.zuoright.com/20230823121213.png)

比如选择连接 Jmeter，可以看到它的使用情况

![20230823121321](https://image.zuoright.com/20230823121321.png)

![20230823121452](https://image.zuoright.com/20230823121452.png)

## JSR

JSR, Java Specification Request 是 Java 社区提出的一种标准化 Java 技术的过程。

定义了 Servlet、JavaSE、Java EE 等各种 Java 平台相关的新功能、API、规范、技术标准等。

目的是确保 Java 技术在不同的实现中保持一致性，以促进 Java 生态系统的互操作性和可移植性。

JSR223 允许在 Java 应用程序中嵌入各种脚本语言，例如 Groovy、Python、Ruby 和 JavaScript 等。它提供了一种通用的接口，使开发人员可以使用多种脚本语言编写、编译和执行脚本，而无需修改应用程序的代码。

JSR388 定义了 JavaSE 13 平台的规范，包括新的语言功能和库改进。
