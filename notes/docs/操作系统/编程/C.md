# C

[C语言免费教程](https://wangdoc.com/clang/intro.html){ .md-button .md-button--primary }

`hello.c`

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
gcc -c hello.c  # 生成目标文件hello.o
gcc -o hello hello.o  # 生成可执行文件hello.out

# 也可以直接编译成可执行文件(a.out)
# 但通常源代码文件不止一个，无法直接编译，所以需要先生成目标文件再一起编译为可执行文件
gcc hello.c
```

传统的编译器通常分为三个部分

- 前端(frontEnd)：词法和语法分析，将代码转化为抽象的语法树
- 优化器(Optimizer)：对前段生成的中间代码进行优化
- 后端(backEnd)：将优化的中间代码转换为针对各自平台的机器码

支持C语言的编译器有

- GCC(GNU Collection) GNU编译器套装
- LLVM(Low Level Virtual Machine) 底层虚拟机，Clang是LLVM的前端