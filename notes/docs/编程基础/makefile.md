# Makefile

> <https://seisman.github.io/how-to-write-makefile/overview.html>

Make 是 C/C++ 语言最常用的构建工具，构建规则需要写在 `Makefile` 中

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
make
# 执行make，默认在当前目录下找名字叫Makefile的文件
#   当然makefile也可以，但建议M大写
#   可用-f指定其它名字的文件
# 找到后，默认将第一个目标文件作为最终目标文件，即上面的all
# 然后递归判断依赖文件的更新时间是否新于目标文件，是则重新生成目标文件

# 执行伪目标
make clean
```
