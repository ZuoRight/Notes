# 引言

CPU只能运行二进制的数据，所以用高级语言编写的源码，需要经过编译(Compile)转换为二进制可执行文件

比如 C/C++ 会把源文件编译成中间码文件(Object File)，在UNIX下文件后缀为`.o`，在Windows下是`.obj`，然后再把大量的中间码合成可执行文件，即链接

## 工作过程

- 配置：确定当前系统环境，通常由`autoconf`工具生成`configure`脚本
  - 标准库和头文件的位置
  - 软件的安装位置
  - 需要安装哪些组件
- 构建：即编译的安排，根据依赖关系等确定先编译这个还是先编译那个，可使用make等构建器完成
- 编译：由编译器完成
  - 预编译头文件
  - 预处理：替换源码中的头文件和宏等
  - 编译：生成机器码
  - 静态连接：编译时链接，即把外部函数库拷贝到可执行文件中
  - 安装
  - 操作系统连接
  - 生成安装包
- 动态连接，运行时链接，运行时引用依赖库
  - Linux平台：`.so`文件
  - Windows平台：`.dll`文件
  - Mac平台：`.dylib`文件

```shell
./configure  # 生成 makefile 文件
make
make install  # 安装&操作系统连接
```

![20221119003533](http://image.zuoright.com/20221119003533.png)

> objcopy 将一种格式的目标文件转换成另一种格式的目标文件

## 编译器

传统的编译器通常分为三个部分

- 前端(frontEnd)：词法和语法分析，将代码转化为抽象的语法树
- 优化器(Optimizer)：对前段生成的中间代码进行优化
- 后端(backEnd)：将优化的中间代码转换为针对各自平台的机器码

![20221119003207](http://image.zuoright.com/20221119003207.png)

### GCC(GNU Collection) GNU编译器套装

```shell
# 从源码直接生成可执行文件hello.out
gcc hello.c  # 可以指定用哪个C标准来编译，比如：-std=c99

# 通常源代码文件不止一个，所以需要先各自生成目标文件再按依赖关系统一编译为可执行文件
gcc -c hello.c  # 先生成目标文件hello.o
gcc -o hello.out a.o b.o 。# -o指定编译后生成的文件名（可以不加.out后缀）

# 编译目标路径所有文件
gcc -o demo *.o
gcc -o demo *.c

./hello.out  # 运行

# 转成汇编
gcc -S example.c
```

### Clang

Clang 一个基于 LLVM 的编译器前端，支持 C/C++/Objective-C 等语言。其开发目标是替代 GCC。

LLVM(Low Level Virtual Machine) ，是当今炙手可热的编译器基础框架。它从一开始就采用了模块化设计的思想，使得每一个编译阶段都被独立出来，形成了一系列的库。LLVM 使用面向对象的 C++ 语言开发，为编译器开发人员提供了易用而丰富的编程接口和 API。

## 参考

- [编译器的工作过程·阮一峰](https://www.ruanyifeng.com/blog/2014/11/compiler.html)
- [程序的运行过程·极客时间](https://time.geekbang.org/column/article/369457)
- [几行汇编几行C·极客时间](https://time.geekbang.org/column/article/369502)
- [高级语言怎样抽象执行逻辑·极客时间](https://time.geekbang.org/column/article/557209)
