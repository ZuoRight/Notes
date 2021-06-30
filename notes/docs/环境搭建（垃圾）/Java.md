# Java

[教程](https://www.liaoxuefeng.com/wiki/1252599548343744/1255883772263712)

[Maven](https://www.liaoxuefeng.com/wiki/1252599548343744/1255945359327200)

JDK(Java Development Kit)：[Java开发工具包](https://www.oracle.com/java/technologies/javase-downloads.html)

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
export JAVA_HOME=/usr/libexec/java_home
export PATH=$PATH:$JAVA_HOME/bin
```

- Windows

```shell
# 先添加一个JAVA_HOME的环境变量，指向jdk根目录
# 再添加Path
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
