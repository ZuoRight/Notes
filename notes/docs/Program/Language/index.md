# 引言

编程语言作为程序员控制一台计算机工作的协议，具备了协议的三要素：

- 语法：就是这一段内容要符合一定的规则和格式
- 语义：就是这一段内容要代表某种意义
- 顺序：就是先干啥，后干啥

## 编程语言分类

![20240608140715](https://image.zuoright.com/20240608140715.png)

[图片来源](https://blog.xnsio.com/2011/07/11/dynamic-typing-is-not-weak-typing/)

声明（Declaration）变量时，是否需要指定类型

- Y：静态类型，编译时检查类型，C, Java, TS
- N：动态类型，运行时检查类型，Python, JS

运行时是否可以隐式转换变量类型

- Y：弱类型，C, PHP, JS
- N：强类型，Java, Go, Python

## 变量

变量命名规则：不能是系统保留关键字，大多数编程语言变量命名都只能由英文、下划线（`_`）、数字（不能作为开头）构成

- 下划线命名法：用于变量名、函数名、模块名等
- 驼峰命名法：主要用于类名

常量命名规范：如果常量在声明前就已知了，通常可以用全大写来表示，如果需要计算得出，那么可以跟变量名一致，Python 中常量其实也是变量，JS 等语言中可能需要单独的关键字声明。

关于大小写敏感，只有少数编程语言大小写敏感，比如 C，其它绝大多数都是不敏感的。

但是建议始终保持大小写一致，养成良好的编程习惯。一般全大写表示这个变量约等于常量。

### Java

``` java
int x = 1;  // 未赋值时初始值为：0
```

### Python

```python
# 变量不需要关键字声明
x = None  # 初始值可以赋值为：None
x = 1
x = "demo"

# 强类型
print(1 + '1')  # TypeError

# 获取变量内存地址
id(x)

# 获取变量所占内存大小
import sys
sys.getsizeof(x)

# 两个数学常量
import math
math.pi  # 3.141592653589793
math.e  # 2.718281828459045
```

在 Python 中，将一个对象赋值给一个变量，并不表示拷贝对象给变量，而是让一个变量指向了一个对象，而一个对象可以被指向多个变量。

另外，变量可以被删除，但对象无法被删除，只能被垃圾回收。

### JavaScript

变量需要声明，但不需要指定类型

ES6 有三个关键字可以用来声明变量：`var`, `let`, `const`

JS 早期设计失误，并未强求用 var 声明，没有被 var 申明默认为全局变量，会带来一定的麻烦，后来推出严格模式（源码第一行加上 `'use strict';`），强制 var 申明，在 `class` 和 `module` 中的代码会默认使用严格模式

不过 ES6 之后推荐使用 let 和 const 来声明

``` javascript
var x = 1;  // var是ES6前的关键字，在函数外声明的就是全局变量，函数内声明的为局部变量，如果重复声明内部变量会覆盖外部变量

let x = 1;  // let是用来改善var的，作用域为{}，同作用域内不允许重复声明，声明变量可以只声明不赋值，未赋值默认为 undefined
let x = "hello world"  // JS语句可以用`;`结尾，也可以忽略
const PI = 3.14;  // const用于声明常量，作用域也是{}，声明常量必须初始化赋值，且不能重新赋值

// ES6新增，解构赋值：同时给多个变量赋值
let [x, y, z] = ['hello', 'JavaScript', 'ES6'];
let {x, y} = {x: 1, y: 2}

console.log(1 + '1');  // "11"
```

### TypeScript

```typescript
let num1: number = 1;
let str: string = 'str';

console.log(1 + '1');  // "11"
```

## 基本数据类型

基本数据类型是 CPU 可以直接进行运算的类型

- 整数：`byte`, `short`, `int`, `long`
- 浮点数：`float`, `double`
- 单个字符：`char`
- 布尔值：`bool`

不同语言有各自的数据类型定义，占用空间、取值范围、默认值都有所不同

比如在 Python 中

```text
整数类型只有 int，它可以是任意大小
浮点数只有 float，但其是双精度 64 位的
没有表示单个字符串的 char 类型，可以用长度为 1 的 str 类型表示
```

## 语句

## 注释

- `//` 注释符基本上被 C 语言家族所用
- `#` 注释符则基本上是被 Shell 和其它脚本语言所用，比如 Python，但 JS 不是

### 终止符

> 参考：[编程语言中分号的简明历史](https://mp.weixin.qq.com/s/VLJZjMp1OuMDwIiL4NH1_g)

常见的编程语言（C、Java、JS等）基本都使用了 `;` 分号，既可以作为分割符（满足一行多句），又可以作为终止符（满足一句多行）

在 Python 中，使用换行符来终止语句，可使用反斜杠满足一句多行（实际就是把换行符转义了），建议使用括号来换行。

### 代码块

> 参考：[编程语言中花括号的简明历史](https://mp.weixin.qq.com/s/8-DgLMBfWSnR0j8Q83UzeQ)

从最初的 `BEGIN...END`，演变为 `$...$`，然后到 C 语言简化为 `{...}`，很多类 C 语言基本都沿用了花括号

Python 中使用了冒号和缩进来表示代码块，而不是花括号，更加简洁

JS 中有时可以省略花括号，比如 if 语句、for 语句、while 语句

### 运算符

在 C/C++、Java、Solidity 等很多语言中，除法（`/`）都是取整，而在 Python、JS 等语言中是正常除

Python 中有单独的取余（`%`）和取整（`//`）运算符

JS 中也有取余（`%`）运算符，但取整需要使用 `Math.floor()` 函数

## 编程范式

编程范式是指在编写和组织计算机程序时采用的基本风格或方法论。

编程语言发展到今天，出现了好多不同的代码编写方式，但不同的方式解决的都是同一个问题，那就是如何写出更为通用、更具可重用性的代码或模块。

![20240608195520](https://image.zuoright.com/20240608195520.png)

虽然很多语言支持多种编程范式，但通常主要使用其中一种，最流行的当属面向对象编程。

![20240608195636](https://image.zuoright.com/20240608195636.png)

图片来源：极客时间《左耳听风·编程范式》

### 声明式编程

SQL、HTML、正则表达式

```sql
SELECT name FROM employees WHERE department = 'Sales';
```

### 命令式编程

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

### 函数式编程

强调使用函数作为主要构建单元，专注于函数的纯粹性、不可变性和组合能力

Lisp、Haskell

```haskell
square x = x * x
main = print (map square [1, 2, 3, 4, 5])  -- 输出: [1, 4, 9, 16, 25]
```

### 面向对象编程

Object-Oriented Programming, OOP

封装、继承、多态

C++、Java、Python

### 泛型编程

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

### 元编程

Meta Programming，能够把程序当做数据来处理，从而让程序产生程序

狭义上讲，编写编译器、汇编器、解释器、链接器、加载器、调试器，才是真正具备元编程能力

而通常我们说某个语言的元编程能力，只是利用语言本身的特性来操纵程序。

- 自省（introspection）一门语言写的程序，能够报告它自身的信息
- 反射（reflection）自省之上，还可以操纵自身

## 设计模式

![20240608200150](https://image.zuoright.com/20240608200150.png)

图片来源：极客时间《设计模式之美》
