# 面向对象编程

Java 是一种面向对象的编程语言，程序的基本单位就是类，所有的代码必须在类的上下文中定义和执行。

一个 `.java` 文件可以定义多个类，但只能有一个 `public` 修饰的公开类，且其类名必须与文件名一致。

```java
/**
 * 特殊的多行注释
 * 需要写在类和方法的定义处，可以用于自动创建文档
 * @auther test
 */

public class Hello {
    /*
    Java 规定程序必须从 main() 开始
    而且必须是 static 静态的
    且参数必须是 String[] 数组
    void 为返回值类型，表示没有返回值
    */
    public static void main(String[] args) {
        System.out.println("Hello, world!");  // 代码行以分号结尾，打印一个字符串到屏幕上
    }
}
```

文件名必需保存为与类名完全一致的 `Hello.java`

```shell
javac Hello.java  # 先编译，生成 Hello.class
java Hello  # JVM 会自动查找与 Hello 对应的 .class 运行
'
Hello, world!
'
```

实例化 instance

```java
class Person {
    public String name;
    public int age;
}

// 定义一个引用类型的变量指向new创建的实例
Person zs = new Person();

zs.name = "Zhang San"; // 对字段name赋值
zs.age = 30; // 对字段age赋值
System.out.println(zs.name); // 访问字段name
```
