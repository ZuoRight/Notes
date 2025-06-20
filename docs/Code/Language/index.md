---
comments: true
hide:
  - feedback
---

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

变量命名规则：大多数编程语言变量命名都只能由英文、下划线（`_`）、数字（不能作为开头）构成，不能包含特殊字符，如连字符（-）、空格等，也不能是系统保留关键字。

- 下划线命名法：用于变量名、函数名、模块名等
- 驼峰命名法：主要用于类名

常量命名规范：如果常量在声明前就已知了，通常可以用全大写来表示，如果需要计算得出，那么可以跟变量名一致，Python 中常量其实也是变量，JS 等语言中可能需要单独的关键字声明。

关于大小写敏感，只有少数编程语言大小写敏感，比如 C，其它绝大多数都是不敏感的。

但是建议始终保持大小写一致，养成良好的编程习惯。一般全大写表示这个变量约等于常量。

### Java

``` java
int x = 1;
int x;  // 未赋值时初始值为：0

final double PI = 3.14;  // 常量

// 根据赋值语句自动推断变量类型
var sb = new StringBuilder();  // 等价于 StringBuilder sb = new StringBuilder();
```

### Python

```python
# 变量不需要关键字声明
x = None  # 必须赋值，初始值可以赋值为 None
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

> 能用 const 就用 const，需要变再用 let

``` javascript
var x = 1;  // var是ES6前的关键字，在函数外声明的就是全局变量，函数内声明的为局部变量，如果重复声明内部变量会覆盖外部变量

let x = 1;  // let是用来改善var的，作用域为{}，同作用域内不允许重复声明，声明变量可以只声明不赋值，未赋值默认为 undefined
let x = "hello world"  // JS语句可以用`;`结尾，也可以忽略

const PI = 3.14;  // const用于声明常量，作用域也是{}，声明常量必须初始化赋值，且不能重新赋值

console.log(1 + '1');  // "11"
```

- 解构赋值：同时给多个变量赋值（ES6 新增）

> 只有数组和对象可以被解构赋值

```js
let arr = [2, 4, 6, 8, 10];
const [,,x] = arr  // 6

let obj = {a:1, b:2, c:{x:11, y:22}}
// 传统赋值
const b = obj.b  // 2
const x = obj.c.x  // 11
// 解构赋值
const {b} = obj  // 2
const {x} = obj.c  // x=11
const {x:new_x} = obj.c  // 解构+重命名，new_x=11
// 连续解构赋值，不推荐
const {c:{x}} = obj  // 先解构c再解构x，x=11，c=undefined
const {c:{x:new_x}} = obj  // 连续解构+重命名
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

大多数语言的 for 循环语句都是 C 语言风格，比如 Java、JS 等

```c
for (初始化表达式; 判断表达式; 循环变量处理表达式)
    statement;
```

Python 语言只有 `for ... in ...` 这一种形式，用于遍历可迭代对象

不过各语言都支持 break 和 continue 语句

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
