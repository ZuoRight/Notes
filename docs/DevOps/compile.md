---
comments: true
hide:
  - feedback
---

# Compile

用高级语言编写的源码，需要转换为可执行的二进制文件，才能给 CPU 运行

比如 C/C++ 会把源文件编译成中间码文件(Object File)，然后再汇编成可链接文件，然后再链接合成可执行文件。

> 在 UNIX 下可链接文件后缀为 `.o`，在 Windows 环境下是 `.obj`
>
> objcopy 是将一种格式的目标文件转换成另一种格式的目标文件

![20221119003533](http://image.zuoright.com/20221119003533.png)

- 配置：确定当前系统环境，通常由 `autoconf` 工具生成 `configure` 脚本
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

## 编译器

传统的编译器通常分为三个部分

- 前端(frontEnd)：词法和语法分析，将代码转化为抽象的语法树
- 优化器(Optimizer)：对前段生成的中间代码进行优化
- 后端(backEnd)：将优化的中间代码转换为针对各自平台的机器码

![20221119003207](http://image.zuoright.com/20221119003207.png)

### GCC

GNU Collection，GNU 编译器套装

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

## 构建器

构建过程通常借助 Make、Maven、Gradle 等构建工具自动化完成

Make 是 C/C++ 语言最常用的构建工具，构建规则需要写在 [Makefile](https://seisman.github.io/how-to-write-makefile/overview.html) 中

```makefile
# 定义变量，建议大写，=左右可以有空格
X = registry/zombie-proc:v1

# 目标文件: 依赖文件
all: app-test image

app-test: app-test.c xx.h
  gcc -o app-test app-test.c  # 命令，用tab缩进

image: app-test
  docker build -t ${X} .  # 引用变量

# 伪目标
# 通常用于install、clean等
clean: 
  rm -f *.o app-test
  docker rmi ${X}
```

```shell
# 生成 makefile 文件
configure

make
# 执行make，默认在当前目录下找名字叫Makefile的文件
#   当然makefile也可以，但建议M大写
#   可用-f指定其它名字的文件
# 找到后，默认将第一个目标文件作为最终目标文件，即上面的all
# 然后递归判断依赖文件的更新时间是否新于目标文件，是则重新生成目标文件

# 执行伪目标
make install  # 安装&操作系统连接
make clean  # 清理
```

### 编译源码安装

wget 是 Linux 中类似迅雷下载的工具，通过 HTTP、HTTPS、FTP 等协议下载

```shell
# 下载 tarball 压缩包文件
wget -c https://xxx.tar.gz  # -c 支持断点续传

sudo tar xfvz xxx.tar.gz -C  /usr/local/src  # 解压缩
cd xxx/  # 可以查看安装说明：README或INSTALL
```

```shell
./configure --prefix=/usr/local/openresty  # 自动生成makefile文件
"""
默认会安装到 usr/local，安装完不需要配置环境变量
建议安装到指定目录（方便卸载，直接删除目录），需要配置环境变量
"""

make clean  # 清除旧的目标文件
make [-j2]  # 编译，使用2个逻辑CPU
make check  # 检查
make install  # 将编译完成的文件安装到预定的目录中

# 更新
"""
下载 patc 文件，更新源代码
更新源代码后，需要重新编译：make
"""

# 删除
rm -rf 软件目录
```

## 参考

- [编译器的工作过程·阮一峰](https://www.ruanyifeng.com/blog/2014/11/compiler.html)
- [程序的运行过程·极客时间](https://time.geekbang.org/column/article/369457)
- [几行汇编几行C·极客时间](https://time.geekbang.org/column/article/369502)
- [高级语言怎样抽象执行逻辑·极客时间](https://time.geekbang.org/column/article/557209)
