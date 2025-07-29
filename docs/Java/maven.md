
# Maven

> <https://www.liaoxuefeng.com/wiki/1252599548343744/1255945359327200>

Java 项目构建和依赖管理工具，可以自动化构建、测试、打包和部署项目

```shell
mvn -v
mvn package
```

idea、ecplise 等不同工具默认的构建格式要求不同，所以统一选择使用 maven 构建可以基于任何工具开发

- 配置文件：`pom.xml `

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!-- 一个 Maven 工程由以下三个字段唯一标识，即可被其它库引入：groupId:artifactId:version -->
    <!-- com.example:demo:1.0-SNAPSHOT -->
    <groupId>com.example</groupId>  <!-- 最多4级：com.{公司}.业务线.子业务线 -->
    <artifactId>demo</artifactId>  <!-- 单体项目可以随便起，微服务一般格式：产品线名-模块名，通常跟项目名保持一致 -->
    <version>1.0-SNAPSHOT</version>  <!-- 通常3级：主版本号.次版本号.修订号，一般工具会默认配置 -->

    <!-- Packaging 属性指示将项目打包为什么类型的文件，比如 idea 会据此识别 Maven 项目类型 -->
    <packaging>war</packaging>
    <!-- 
        jar 代表普通的 Java 工程，打包后是 .jar 结尾的文件，不写的时候默认就是这个
        war 代表 Java Web 工程，打包以后是 .war 结尾的文件
        pom 代表不会打包，用来做继承的父工程
    -->

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

项目结构

```shell
path/
    project1/
    project2/
    project3/
        ├── pom.xml                  # Maven 配置文件
        ├── src
        │   ├── main
        │   │   ├── java             # Java 源代码目录
        │   │       └── com/example/myapp/    # 开发者代码主目录，主类（入口）Main.java
        │   │               controller
        │   │               service
        │   │               dao
        │   │               model        
        │   │   ├── resources        # 存放静态资源、配置文件等
        │   │           log4j.properties
        │   │           spring-mybatis.xml
        │   │           static/
        │   │               css/
        │   │               js/
        │   │               images/
        │   │   ├── webapp           # Java Web 标准结构，不能乱改
        │   │       └── WEB-INF
        │   │          └── web.xml
        │   └── test
        │       └── java             # 测试源码目录
        │       └── resources        # 测试资源目录
        ├── target                   # 所有编译、打包生成的文件会放在这里
        │   ├── classes
        │   │   ├── com/example/       # Java 编译后的字节码文件，比如 Main.class
        │   └── test-classes
```
