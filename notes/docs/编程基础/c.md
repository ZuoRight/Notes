# C

[C语言免费教程](https://wangdoc.com/clang/intro.html){ .md-button .md-button--primary }

C的第一个标准是由ANSI(美国国家标准协会)1989年发布的。虽然这份文档后来被ISO(国际标准化组织)采纳并且ISO发布的修订版亦被ANSI采纳，但名称ANSI C仍被广泛使用。一些软件开发者使用名称ISO C，还有一些使用中立的名称Standard C。

- C89
- C99
- C11
- C18

## C/C++开发环境：C99标准

- Windows IDE: Dev C++（内嵌了gcc编译器的移植版）：安装时选择English，第一次启动后再设置为中文
- Mac: vim + gcc
- VSCode: C/C++插件

## `hello.c`

```c
#include <stdio.h>  //引入<标准输入输出>头文件
#include <math.h>  //引入函数库
int main(void){  //main函数为程序主入口
    printf("hello wolrd\n");
    float value;
    value = sin(3.14/2);
    printf("%f\n", value);
}
```

```bash
# 可以直接编译成可执行文件(a.out)
gcc hello.c
"""
但通常源代码文件不止一个，无法直接编译
所以需要先生成目标文件再一起编译为可执行文件
"""
gcc -c hello.c  # 生成目标文件hello.o
gcc -o hello hello.o  # 生成可执行文件hello.out
```

## 编译

> 编译(compile)：指将编程语言编写的源代码通过编译器转换为可执行文件

传统的编译器通常分为三个部分

- 前端(frontEnd)：词法和语法分析，将代码转化为抽象的语法树
- 优化器(Optimizer)：对前段生成的中间代码进行优化
- 后端(backEnd)：将优化的中间代码转换为针对各自平台的机器码

支持C语言的编译器有

- GCC(GNU Collection) GNU编译器套装
- LLVM(Low Level Virtual Machine) 底层虚拟机，Clang是LLVM的前端

## Make

> 构建(build)：先编译这个还是先编译那个，即编译的安排

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
