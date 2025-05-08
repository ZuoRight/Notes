# 引言

[廖雪峰 Java 教程](https://www.liaoxuefeng.com/wiki/1252599548343744/1255883772263712)

Java 最初由 SUN 公司从 Oak 语言改造而来，后被 Oracle 收购

Java 将代码编译成一种字节码，然后不同平台的虚拟机（JVM）负责加载并执行字节码，即所谓的一次编写，到处运行

![20230925100617](https://image.zuoright.com/20230925100617.png)

- JavaME 是一个裁剪版的 JDK，现在使用很少
- JavaSE, Java Platform Standard Edition 标准版，包含 JVM 和标准库
- JavaEE, Java Platform Enterprise Edition 企业版，最早叫 J2EE，现在叫 Jakarta EE，是在 JavaSE 的基础上多了一些服务器相关的库和 API，以便开发 Web 应用、数据库、消息服务等，最核心的组件就是基于 Servlet 标准的 Web 服务器

## JSR

JSR, Java Specification Request 是 Java 社区提出的一种标准化 Java 技术的过程。

是一系列标准化的规范，从 JVM 的内存模型到 Web 程序接口，目的是确保 Java 技术在不同的实现中保持一致性，以促进 Java 生态系统的互操作性和可移植性。

负责审核 JSR 的组织叫 JCP

JSR223 允许在 Java 应用程序中嵌入各种脚本语言，例如 Groovy、Python、Ruby 和 JavaScript 等。它提供了一种通用的接口，使开发人员可以使用多种脚本语言编写、编译和执行脚本，而无需修改应用程序的代码。

JSR388 定义了重要版本 JavaSE 13 平台的规范，包括新的语言功能和库改进。

## 环境搭建

IDE

- Eclipse
- IntelliJ Idea
- VSCode + Extension Pack for Java

![20210630131230](https://image.zuoright.com/20210630131230.png)

- JRE, Java Runtime Environment
- JDK, [Java Development Kit](https://www.oracle.com/java/technologies/downloads/)

有些软件在高版本 JDK 环境下会有各种莫名其妙的问题，可以使用较稳定的 Java 8，即 [Java 1.8](https://www.oracle.com/java/technologies/downloads/?er=221886#java8)

其它 LTS(Long Term Support) 版本有：JDK 11、17

### Mac

```shell
# Java
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-16.0.1.jdk/Contents/Home  # Library 即资源库
# 拼接到 PATH 路径字符串后面或前面都行，冒号分割
export PATH=$PATH:$JAVA_HOME/bin
```

### Windows

```shell
# JAVA_HOME
C:\Program Files\Java\jdk-11.0.12

# Path
%JAVA_HOME%\bin;
%JAVA_HOME%\jre\bin;
```

## 构建工具

Java 程序的构建过程一般是：编译、测试、打包。

![20240725214548](https://image.zuoright.com/20240725214548.png)

如果文件比较少，我们可以使用以下命令手动构建

- jdb 调试器，用于开发阶段的运行调试
- javac 编译器，把 `.java` 源码编译为 `.class` 字节码
- java 启动 JVM 执行编译后的代码
- jar 把一组 `.class` 文件打包成 `.jar` 包，便于发布

```shell
# 查看编译器版本
javac -version
'
javac 22.0.2
'

# 查看虚拟机版本
java -version
'
java 22.0.2 2024-07-16
Java(TM) SE Runtime Environment (build 22.0.2+9-70)
Java HotSpot(TM) 64-Bit Server VM (build 22.0.2+9-70, mixed mode, sharing)
'
```

但当工程越来越大，文件越来越多，这些机械重复的工作就需要交给工具来完成了：

- Ant 与 Makefile 比较像，定义任务，规定依赖，执行任务，缺点是没办法管理依赖
- [Maven](https://www.liaoxuefeng.com/wiki/1252599548343744/1255945359327200) 提出仓库的概念，缺点是使用 xml 语法不简洁，无法自定义任务
- Gradle 继承 Maven 和 Ant 的优点，可以用仓库管理依赖也能自定义任务，`build.gradle` 基于脚本语言 Groovy

### Maven

使用 Maven 时，基本上只会用到 mvn 这一个命令：`mvn -version`

标准结构，不要乱改

```text
project/
├── src/
│   ├── main/
│   │   ├── java/            # Java 源代码目录
│   │   │   └── com/example/Main.java    # 主类（入口）
│   │   ├── resources/       # 配置文件目录（如 application.properties）
|   |
│   └── test/
│       └── java/            # 测试源码目录
│       └── resources/       # 测试资源目录
|
├── target                   # 所有编译、打包生成的文件会放在这里
│   ├── classes/
│   │   ├── com/example/Main.class       # Java 编译后的字节码文件
│   └── test-classes/
|
├── pom.xml                  # Maven 配置文件
```

- `pom.xml `

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!-- 一个Maven工程由以下三个字段唯一标识，即可被其它库引入：groupId:artifactId:version -->
    <!-- com.example:demo:1.0-SNAPSHOT -->
    <groupId>com.example</groupId>  <!-- 类似于 Java 包名，通常用公司或组织名称 -->
    <artifactId>demo</artifactId>  <!-- 类似于 Java 类名，通常是项目名称 -->
    <version>1.0-SNAPSHOT</version>  <!-- 版本号 -->

    <!-- 固定JDK版本，防止同一个项目的不同的开发者各自使用不同版本的JDK -->
    <properties>
        <maven.compiler.source>17</maven.compiler.source>  <!-- 表示Java编译器读取的源码版本 -->
        <maven.compiler.target>17</maven.compiler.target>  <!-- 表示Java编译器编译的Class版本 -->
        <!-- maven.compiler.release 使用的JDK版本 -->
    </properties>

    <!-- 声明依赖，Maven会自动下载这个依赖包并把它放到classpath中 -->
    <dependencies>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-simple</artifactId>
            <version>2.0.16</version>
        </dependency>
	</dependencies>
</project>
```

## Java Web

Java Web 相关的标准都是在 EE 中定义的

基本架构：Client → Web Server (如 Apache) → Tomcat (Servlet 容器) → Servlet/JSP → 业务逻辑

演进路线：Servlet → JSP/JavaBeans → Struts → Spring MVC → Spring Boot → Spring Cloud

### WAR

Web Application Archive

![20250416203124](https://image.zuoright.com/20250416203124.png)

普通的 Java 程序是通过启动 JVM，然后执行 `main()` 开始运行。而 Java Web 应用程序，需要启动 Servlet 容器加载 WAR 包来运行 Servlet

WAR 包是 Java web 应用的完整部署单元，将 WAR 包部署到 Tomcat 等 Servlet 容器中，容器会解析其中的 Servlet 并使其可用

```text
MyWebApp.war
├── META-INF/           # 包含应用元数据
│   └── MANIFEST.MF     # 应用清单文件
├── WEB-INF/            # 包含非公开访问的资源
│   ├── web.xml         # 部署描述文件（配置Servlet等，定义了 Servlet 的映射规则）
│   ├── classes/        # 编译后的Java类文件（包括Servlet）
│   │   └── com/
│   │       └── example/
│   │           └── MyServlet.class
│   └── lib/            # 依赖的JAR包
│       └── dependency.jar
├── index.jsp           # 公开可访问的JSP文件
├── images/             # 静态资源
└── css/                # 样式文件
```

### Servlet + JSP + JavaBean

![20250416201618](https://image.zuoright.com/20250416201618.png)

Servlet 是 Java Web 开发的起点，提供了处理 HTTP 请求的底层 API。

```java
import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class SimpleServlet extends HttpServlet {
    public void doGet(HttpServletRequest request, HttpServletResponse response) throws IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<html><body>");
        out.println("<h1>Hello World</h1>");
        out.println("</body></html>");
    }
}
```

Servlet 一行行拼接生成 HTML，效率太低，于是有了 JSP(Java Server Pages) 动态网页技术，可以在 HTML 中嵌入 Java 代码用于创建动态网页，本质是 Servlet 的语法糖。

常用的 Servlet 容器有

- `Jetty`：由 Eclipse 开发的开源免费服务器
- `Tomcat`：由 Apache 开发的开源免费服务器
- `GlassFish`：一个开源的全功能 JavaEE 服务器

### Spring

Servlet 的痛点：大量的样板代码，需要手动解析请求参数和管理响应，控制器逻辑与视图逻辑混合，复杂的 XML 配置，难以进行单元测试

Spring MVC 作为 Spring 框架的一部分，带来了结构化的 MVC 模式，显著简化了 Web 开发。

Spring 框架的核心概念

- IoC 通过将依赖管理转移给框架，减少组件间耦合，增强可测试性和灵活性
- AOP 允许横切关注点的模块化，减少重复代码，增强代码的可维护性

但仍存在问题：需要大量配置 XML (应用上下文、视图解析器等)、项目搭建和依赖管理复杂、需要手动部署到外部 Servlet 容器 (Tomcat)

Spring Boot 是一个基于 Spring 的套件，预组装了 Spring 的一系列组件（比如内嵌了 Servlet 容器），以便以尽可能少的代码和配置来开发基于 Spring 的 Java 应用程序。

Spring Cloud 是基于 Spring Boot 的分布式云开发框架

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
