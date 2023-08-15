# Java

[教程](https://www.liaoxuefeng.com/wiki/1252599548343744/1255883772263712)

JDK(Java Development Kit)：[Java开发工具包](https://www.oracle.com/java/technologies/javase-downloads.html)

![20210630131230](http://image.zuoright.com/20210630131230.png)

- java 可执行程序，其实就是JVM
- jdb 调试器
- javac 编译器，把源码(`.java`)编译为字节码(`.class`)
- jar 把一组`.class`文件打包成`.jar`包

## 设置环境变量

```shell
java -version
javac -version
```

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

- 代码

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

- 运行

```shell
javac Hello.java  # # 先编译，生成demo.class
java Hello  # 参数Hello为类名，JVM会自动查找对应的.class并运行
```

## 构建工具

Java程序的构建过程一般是：编译、测试、打包。

如果文件比较少，我们可以手动使用javac、java、jar这些命令手动构建

但当工程越来越大，文件越来越多，这些机械重复的工作就需要交给工具来帮我们完成了：

- ant 与makefile比较像，定义任务，规定依赖，执行任务，缺点是没办法管理依赖
- [Maven](https://www.liaoxuefeng.com/wiki/1252599548343744/1255945359327200) 提出仓库的概念，缺点是使用xml语法不简洁，无法自定义任务
- gradle 继承maven和ant的优点，可以用仓库管理依赖也能自定义任务，`build.gradle`基于脚本语言Groovy

## JavaEE

- Servlet+JSP+JavaBean，容器：Tomcat
- Spring MVC，容器：IoC
- Spring Boot：开箱即用
- Spring Cloud
