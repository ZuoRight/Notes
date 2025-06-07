# 基础语法

Java 是一种面向对象的编程语言，程序的基本单位就是 class，所有的代码必须在类的上下文中定义和执行。

```java
/**
 * 特殊的多行注释
 * 需要写在类和方法的定义处，可以用于自动创建文档
 * @auther test
 */

/* 
定义一个公开的类，一个文件只能有一个 public class
public 是访问修饰符
*/
public class Hello {
    /*
    Java 规定程序必须从 main() 开始，而且必须是静态的，且参数必须是 String 数组
    static 表示静态方法，void 为返回值类型，表示没有返回值，String[] 是参数类型
    */
    public static void main(String[] args) {
        System.out.println("Hello, world!");  // 代码行以分号结尾，打印一个字符串到屏幕上
    }
}
```

必需保存为与类名完全一致的文件名：`Hello.java`

运行

```shell
javac Hello.java  # 先编译，生成 Hello.class
java Hello  # JVM 会自动查找与 Hello 对应的 .class 运行
'
Hello, world!
'
```

## 基本类型

不同数据类型占用的字节数不一样

```text
整数类型：最高位表示符号位
    byte 1字节
    short 2字节
    int 4字节
    long 8字节
浮点数类型
    float 4字节
    double 8字节
布尔类型
    boolean 4字节
字符类型
    char 2字节
```

```java
int i = 15;
int i = 0xf;
int i = 0b1111;

float f = 3.14f;
double d = 1.79e308;

boolean b1 = true;
boolean b2 = false;

// 字符，单引号，保存一个Unicode字符
char a = 'A';  // 等价于 char a = '\u0041';
char zh = '中';
```

## 引用类型

类似于 C 语言的指针，它内部存储一个地址，指向某个对象在内存的位置

### 字符串

```java
// 字符串，双引号，保存任意个字符
String s = null;  // 空值
String s = "";  // 空字符串
String s = "hello";
// 多行字符串
String s = """
    SELECT * FROM
        users
    WHERE id > 100
    ORDER BY name DESC
""";
```

### 数组

存储一组相同类型的值

数组所有元素初始化为默认值，整型都是 `0`，浮点型是 `0.0`，布尔型是 `false`

```java
// 初始化之后大小不可变
int[] ns = new int[5];
ns[0] = 11;
ns[1] = 22;

// 也可以初始化的时候同时指定元素，编译器会自动推算数组大小
int[] ns = new int[] { 11, 22, 91, 85, 62 };
// 简写
int[] ns = { 68, 79, 91, 85, 62 };

ns.length  // 获取长度
```

```java
// 字符串数组
String[] names = {
    "ABC", "XYZ", "zoo"
};
```

## 语句

### 输出

`System.out` 标准输出流

```java
double d = 12900000;
// print 不换行
System.out.print();
// print line 结尾会换行\n
System.out.println(d);  // 1.29E7
// 格式化输出，两个 %% 表示 %
System.out.printf("%.2f\n", d);  // 3.14
```

### 输入

可以直接使用标准输入流 `System.out` 获取用户输入，但需要复杂的代码，而通过 `Scanner` 可以简化后续的代码，并且会自动转换数据类型

读取不同类型需要不同的方法

- `scanner.nextInt()`
- `scanner.nextDouble()`
- `scanner.nextLine()` 读取字符串

```java
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        // 创建 Scanner 对象
        Scanner sc = new Scanner(System.in);

        System.out.print("Input your name: ");  // 打印提示
        String name = scanner.nextLine();  // 读取一行输入，并获取字符串

        System.out.print("Input your age: ");  // 打印提示
        int age = scanner.nextInt();  // 读取一行输入，并获取整数

        // 格式化输出
        System.out.printf("Hi, %s, you are %d\n", name, age);
    }
}
```

### if

```java
if (n >= 90) {
    System.out.println("优秀");
} else if (n >= 60) {
    System.out.println("及格了");
} else {
    System.out.println("挂科了");
}
System.out.println("END");

// 当{}内只有一行语句时，可以省略{}，但不建议
if (n >= 60)
    System.out.println("及格了");
```
