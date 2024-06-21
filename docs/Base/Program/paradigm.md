# 编程范式

编程范式是指在编写和组织计算机程序时采用的基本风格或方法论。

编程语言发展到今天，出现了好多不同的代码编写方式，但不同的方式解决的都是同一个问题，那就是如何写出更为通用、更具可重用性的代码或模块。

![20240608195520](https://image.zuoright.com/20240608195520.png)

布朗大学的计算机科学教授 Shriram 曾在其一篇论文开头中大概表达说，编程范式是旧时代的遗留物，令人厌烦，现代语言的设计者按照自己的意愿从别处借鉴特性，创建出完全无视过往的大杂烩，比如 Python，所以不要试图把语言归为某一类，相反，把它们视作特性的聚合更有用。

从极客时间《左耳听风·编程范式》里这张图可以看出，最流行的特性当属面向对象编程

![20240608195636](https://image.zuoright.com/20240608195636.png)

## 声明式编程

SQL、HTML、正则表达式

```sql
SELECT name FROM employees WHERE department = 'Sales';
```

## 命令式编程

通过一系列指令改变程序状态的编程范式：变量、赋值、循环、控制结构

C 语言

```c
#include <stdio.h>

int main() {
    int sum = 0;
    for (int i = 1; i <= 5; i++) {
        sum += i;
    }
    printf("Sum: %d\n", sum);  // 输出: Sum: 15
    return 0;
}
```

## 函数式编程

Lisp 是最早的函数式语言，lambda, map, filter, reduce 都是 Lisp 中先出现的

Haskell 是一个纯函数式编程语言，函数是没有变量的，强调使用函数作为主要构建单元，专注于函数的纯粹性、不可变性和组合能力。

> [函数式编程初探·廖雪峰](http://www.ruanyifeng.com/blog/2012/04/functional_programming.html)


```haskell
square x = x * x
main = print (map square [1, 2, 3, 4, 5])  -- 输出: [1, 4, 9, 16, 25]
```

Guido 明确表示 Python 虽然把函数定位一等对象，但他并不把 Python 当作一个函数式编程语言。

Python 中可以使用 operator 和 functools 等包实现函数式编程风格

## 面向对象编程

Object-Oriented Programming, OOP

封装、继承、多态

C++、Java、Python

## 泛型编程

泛型（Generics）是一种在编程语言中用于创建可重用代码的机制。它允许在定义类、接口和方法时，可以使用不同类型的参数进行操作，从而提高代码的灵活性和复用性。

主要是一些静态类型语言中会用到，比如 Java、TS。

```java
// 比如想用 Java 的 ArrayList 存储不同的数据类型
// 则需要给每种类型都单独定义一个类，比如要存储 String
public class StringArrayList {
    private String[] array;
    private int size;
    public void add(String e) {...}
    public void remove(int index) {...}
    public String get(int index) {...}
}

// 而使用泛型，则可以定义一种模板，例如 ArrayList<T>，T 可以是任何类型
public class ArrayList<T> {
    private T[] array;
    private int size;
    public void add(T e) {...}
    public void remove(int index) {...}
    public T get(int index) {...}
}

// 然后便可以使用 ArrayList<类型> 创建任意类型的类，而无需为每种类型单独编写代码
ArrayList<String> strList = new ArrayList<String>();
// 这样一来，既实现了编写一次，万能匹配，又通过编译器保证了类型安全
```

## 元编程

Meta Programming，能够把程序当做数据来处理，从而让程序产生程序

狭义上讲，编写编译器、汇编器、解释器、链接器、加载器、调试器，才是真正具备元编程能力

而通常我们说某个语言的元编程能力，只是利用语言本身的特性来操纵程序。

- 自省（introspection）一门语言写的程序，能够报告它自身的信息
- 反射（reflection）自省之上，还可以操纵自身
