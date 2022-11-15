# 引言

> [C语言免费教程](https://wangdoc.com/clang/intro.html)
>
> [C编程一站式学习](https://akaedu.github.io/book/index.html)

C 语言能够直接操作硬件、管理内存、跟操作系统对话，是一种非常接近底层的语言，性能很高。被设计的目的是为了将Unix系统移植到其他计算机，所以具有良好的可移植性，另外语法规则简单，没有类(class)等高级的数据结构，也没有语法糖。另外限制比较少，比如没有类型检查，负索引检查等，也不会自动清理内存，所以带来的问题是debug不太容易。

C的第一个标准由美国国家标准协会(ANSI)1989年发布，然后国际标准化组织(ISO)发布了修订版，所以即可以叫`ANSI C`，也可以叫`ISO C`，还可以叫`Standard C`，但通常用年份来区分版本，比如这一版则称之为`C89`或`C90`，其他版本如下

- C89或C90
- C95 增加了多字节字符和宽字符
- C99 第一次大型修订，最流行的版本
- C11 增加了Unicode和多线程
- C17或C18 修复了C11的一些缺陷

## C/C++开发环境：C99标准

- Windows IDE: Dev C++（内嵌了gcc编译器的移植版）：安装时选择English，第一次启动后再设置为中文
- Mac: vim + gcc
- VSCode: C/C++插件
- 在线：<https://riju.codes/c>

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

## 预处理

> <https://wangdoc.com/clang/preprocessor>

预处理指令以#开头，为了兼容旧版本编译器，后面一般不留空格

```c
// 最常见的预处理指令：#define，用于替换指定的词，每条替换规则称为一个宏（macro）
#define MAX 100  // 这里的MAX就称为一个宏，用于将源码里面的MAX全部替换成100
#undef MAX  // 取消宏
```

## 编译 compile

> 将编程语言编写的文本源代码通过编译器转换为可执行文件

传统的编译器通常分为三个部分

- 前端(frontEnd)：词法和语法分析，将代码转化为抽象的语法树
- 优化器(Optimizer)：对前段生成的中间代码进行优化
- 后端(backEnd)：将优化的中间代码转换为针对各自平台的机器码

支持C语言的编译器有

- GCC(GNU Collection) GNU编译器套装
- LLVM(Low Level Virtual Machine) 底层虚拟机，Clang是LLVM的前端

C 语言是编译型语言，源码文件需要编译生成可执行文件后执行

```bash
"""
可以直接编译成可执行文件
  Linux平台可执行文件后缀为.out，out是assembler output的缩写，直译为汇编器输出
  Windows平台可执行文件后缀为.exe
但通常源代码文件不止一个，无法直接编译，所以需要先生成目标文件再一起编译为可执行文件
"""
gcc hello.c  # 从源码直接生成可执行文件hello.out，可以指定用哪个C标准来编译，比如：-std=c99
gcc -o demo demoa.c demob.c  # 将两个有关联的文件一起编译，-o指定编译后生成的文件名
gcc -o demo *.c  # 编译目标路径所有.c文件

gcc -c hello.c  # 先生成目标文件hello.o
gcc -o hello hello.o  # 从目标文件生成可执行文件hello，可以不带后缀

./hello.out  # 运行
```

## 构建 build

> 先编译这个还是先编译那个，即编译的安排

Make是C语言最常用的构建工具，需要将构建规则写在makefile中

```makefile
# 定义变量，建议大写，=左右可以有空格
X = registry/zombie-proc:v1

# 目标: 依赖
all: app-test image

app-test: app-test.c
    # 命令
    gcc -o app-test app-test.c

image: app-test
    docker build -t ${X} .  # 引用变量，也可也用$(X)

clean: 
    rm -f *.o app-test
    docker rmi ${X}
```

```bash
make  # 默认构建第一个目标
make image  # 构建指定目标
```

## 内存管理

局部变量所在的内存称为栈(stack)，由系统管理，函数运行时进入内存，运行结束时自动清理

全局变量所在的内存称为堆(heap)，由手动管理，函数运行结束后需要手动释放，否则会内存泄漏(memory leak)
