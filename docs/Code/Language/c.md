# C

> [C语言免费教程](https://wangdoc.com/clang/intro.html)
>
> [C编程一站式学习](https://akaedu.github.io/book/index.html)

1969年，美国贝尔实验室的肯·汤普森（Ken Thompson）与丹尼斯·里奇（Dennis Ritchie）一起开发了 Unix 操作系统。

Unix 是用汇编语言写的，无法移植到其他计算机，他们决定使用高级语言重写。但是，当时的高级语言无法满足他们的要求，汤普森就在 BCPL 语言的基础上发明了 B 语言。

1972年，丹尼斯·里奇和布莱恩·柯林汉（Brian Kernighan）又在 B 语言的基础上重新设计了一种新语言，这种新语言取代了 B 语言，所以称为 C 语言。

1973年，整个 Unix 系统都使用 C 语言重写。此后，C 语言开始快速流传，广泛用于各种操作系统和系统软件的开发。

C 语言能够直接操作硬件、管理内存、跟操作系统对话，是一种非常接近底层的语言，性能很高。被设计的目的是为了将Unix系统移植到其他计算机，所以具有良好的可移植性，另外语法规则简单，没有类(class)等高级的数据结构，也没有语法糖。另外限制比较少，比如没有类型检查，负索引检查等，也不会自动清理内存，所以带来的问题是debug不太容易。

很多现代高级编程语言的底层实现，其实都是基于C或C++的，比如CPython。像C语言这种由自身编写其编译器的则被称为自举（bootstrapping）。

## 标准

C 的第一个标准由美国国家标准协会(ANSI)1989年发布，然后国际标准化组织(ISO)发布了修订版，所以即可以叫 `ANSI C`，也可以叫 `ISO C`，还可以叫 `Standard C`。

通常用年份来区分版本

```text
C89/C90 第一个标准版
C95 增加了多字节字符和宽字符
C99 第一次大型修订，最流行的版本
C11 增加了Unicode和多线程
C17/C18 修复了C11的一些缺陷
```

## 开发环境

- VSCode: C/C++插件
- 在线：<https://riju.codes/c>
- Mac: vim + gcc
- Windows IDE: Dev C++（内嵌了gcc编译器的移植版）：安装时选择English，第一次启动后再设置为中文

## `hello.c`

```c
/*
  多行注释，编译时注释会被转换为一个空格

  C语言自带了一些标准库，根据功能分类，每一类组成一个头文件(.h文件)
  比如要使用输出函数printf()，需要先引入头文件stdio.h

  使用#include命令可引入头文件，标准库放入<尖括号>中，第三方库放在“双引号”中
*/
#include <stdio.h>  // 标准I/O库
#include <math.h>  // 数学函数库

/*
  函数也需要声明并指定返回值类型，如果没有返回值可以用void声明
  如果不需要传入参数也可以使用void
  main函数为程序主入口
*/
int main(void){  // 语句块{}，快内语句可以不缩进，为了美观可以缩进两个或者四个空格
    printf("hello wolrd\n");  // 语句由分号结尾

    /*
      变量是内存中某一块区域的名称，可以通过变量引用这块内存，获取里面的值
      变量名不能以数字、__双下划线、_单下划线加大写字母这样开头

      C语言属于静态类型语言，所以变量必须声明并指定类型后使用
      C属于强类型语言，所以运行时不能修改变量的类型

      作用域：块内的变量属于局部变量，嵌套时，同名内层变量覆盖外层变量
    */
    float x;  // 声明一个浮点型变量x
    int a=1, b=2;  // 声明并赋值，相同类型变量可以一起声明
    int a=b=c=2;  // 为多个变量赋相同的值
    x = sin(3.14/2);
    printf("%f", x);
    /* 
      printf中的f是format的意思
      常见占位符：
        %i 整数，基本等同于 %d
        %f 浮点数，包括float和double
        %s 字符串
      宽度
        %5d 宽度不够默认向左补空格
        %-5d 向右补空格
        %+5d 正整数前面显示表示正负的+号
        ---
        %12f 浮点数默认小数点后6位，先补够小数点后6位再向左补空格
        %.2f 限制浮点数精度
        %12.2f 宽度与精度结合使用
        ---
        %.5s 只输出字符串的前5个字符
        ---
        %*.*f 星号表示占位，即动态传入宽度和精度
    */
    printf("%*.*f\n", 6, 2, 0.5);  // 浮点数0.5以宽度为6精度为2的格式输出
    return 0;  // 返回0表示运行成功，main函数可以省略，其他函数必须要有
}
```

### 预处理

> <https://wangdoc.com/clang/preprocessor>

```c
// 预处理指令以#开头，为了兼容旧版本编译器，后面一般不留空格
// 最常见的预处理指令：#define，用于替换指定的词，每条替换规则称为一个宏（macro）
#define MAX 100  // 这里的MAX就称为一个宏，用于将源码里面的MAX全部替换成100
#undef MAX  // 取消宏
```

## 基本类型

- 整数：short, int, long
- 浮点数：float, double, long double
- 单字符：char
- 空：void

```c
int x = 100;
int x = 0x1A2B; // 十六进制

float x = 10.5;

char x = 'B';  // 等同于：char c = 66;
```

## 字符串

C 语言没有单独的字符串类型，字符串实际是字符（char）类型的数组

```c
char* s = "Hello";  // 指针方式声明，不可以修改，可以指向另一个字符串

char s[] = "Hello";  // 数组的方式声明，可以修改，但是不能指向另一个字符串，{'H', 'e', 'l', 'l', 'o'}

#include <string.h>
printf("%zd", strlen(s));  // 返回字符串字节长度，5
printf("%d", sizeof(s));  // 字节长度，6，每个字符串末尾会自动加一个结束符\0（一个全是二进制0的字节）
```

## 数组

一组按顺序连续存储的同类型值，可变

```c
/*
  声明一个int型数组，数组长度（成员数量）5，即包含5个int类型的值，默认都是0
  声明时可以用变量定义数组长度，即变长数组，长度可以不固定
*/
int scores[5];
scores[0];  // 引用第一个值
scores[3] = 111;  // 给index=3处赋值，注意：越界不会报错

int board[2][5];  // 二维数组

int x[5] = {22, 37, 3490, 18, 95};  // 声明并赋值，此时可省略数组长度

/*
  sizeof可以返回字节长度，用总字节长度除以一个字符的字节长度，即可得到字符长度/数量
  sizeof返回值的数据类型是size_t，需要用%zu或%zd占位
*/
printf("%zu", sizeof(x) / sizeof(int)); // 5=20/4
```

## struct 结构体

C 语言没有类/对象，struct 就类似于其他编程语言的class/object

自定义结构体，可包含不同类型的值

```c
// 自定义数据类型fraction
struct fraction {
  int numerator;
  int denominator;
};

// 声明一个fraction类型的变量f1
struct fraction f1;  // 不能写成fraction f1

// 赋值
f1.numerator = 22;
f1.denominator = 7;
```

## union 共用体

可以变换数据类型的自定义类型

```c
// 自定义union类型，包含三个不同属性，但同时只能取其中一个（最后被赋值的）属性的值
union quantity {
  short count;
  float weight;
  float volume;
};

union quantity q;
q.count = 4;  // 想取某个属性的值，就需要先给它赋值
```

## enum 枚举

```c
// 内部常量名通常大写
enum colors { RED, GREEN, BLUE };
printf("%d\n", RED); // 0
```

## 数据类型起别名

关键字`typedef`可以为数据类型起别名

```c
// 简化，方便引用和提高可读性，比如给自定义类型起别名
typedef struct animal {
  char* name;
  int leg_count, speed;
} animal;

// 方便日后修改变量类型
typedef float app_float;
app_float f1, f2, f3;
typedef long double app_float;  // 修改f1, f2, f3的类型只需要重申typedef语句
```

## 变量说明符

- const

变量变为只读，不可被修改

- static

对于局部变量，每次函数执行结束后不会被系统自动清理，而是保留

对于全局变量，限制变量只能用于当前文件，不能被其他文件引用

## 循环语句

### for 语句

```c
for (int i = 10; i > 0; i--)
    printf("i is %d\n", i);
```
