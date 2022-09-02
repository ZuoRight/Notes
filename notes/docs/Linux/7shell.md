# Shell

[Bash免费教程](https://wangdoc.com/bash/intro.html){ .md-button .md-button--primary }

## 变量

```bash
# 变量赋值，无需声明
x=123  # 赋值符=左右不能有空格
# 值有空格时可使用引号括起来（还可以用反斜杠转义特殊字符：x0=ha\ ha）
x1='hello world'  # 单引号中内容全部视作为纯文本
x2="$x1"  # 双引号中的特殊字符保持原义，比如$

# 取消变量
unset x  # x前不需要加$

# 查看所有变量
set
# 查看所有环境变量
env

# 引用变量
y=${x}  # 可省略花括号
y=$(ls -al)  # 引用命令（推荐）
y=`ls -al`  # 引用命令

# 扩增变量
PATH=$PATH:/home/bin
PATH="$PATH":/home/bin
PATH=${PATH}:/home/bin  # 推荐
```

## 数组

```bash
x = (a, b, c)  # 可以用数组赋多个值，空格分隔

# 显示数组第一个元素
echo ${x[0]}

# 访问数组的所有元素
echo ${x[*]}
echo ${x[@]}

# 获取数组长度
echo ${#x[*]}

# 删除数组中元素
unset x[0]
```

## 输出

```bash
echo $y
echo ${#x}  # 获取变量长度
echo ${x:n:m}  # 切片
```

常用预定义变量(一般都大写)

```bash
echo $USER  # 当前用户
echo $UID  # 当前用户UID
echo $SHELL  # 查看默认Shell
echo $PATH  # 命令搜索路径

# 以下并不是环境变量，只是Shell的操作环境
echo $PS1  # 查看提示字符设置，即[root@localhost ~]内容的设置
echo $?  # 显示上一条命令结果是否正确，正确返回0，否则返回1
echo $$  # 显示当前shell的PID，脚本检测时会用到
echo $0  # 显示当前进程的名称
echo $1
echo ${2-_}  # 如果没有传入位置参数，默认赋值下划线
```

## 输入/输出重定向

```bash
"""
一个进程默认会打开三个文件描述符，通常用数字表示
  0 标准输入stdin，输入自键盘
  1 标准输出stdout，默认输出到终端
  2 标准错误输出stderr，默认输出到终端

> 或 >1 输出重定向，把输出到终端的内容转储到文件
< 或 <0 输入重定向，从文件读取内容替代终端输入
& 表示后台运行
"""
# stdin重定向
wc -l < /etc/xxx  # 统计输入行数

# stdout重定向
cmd > file  # 将命令的输出从屏幕重定向到file文件，file若非空，则默认覆盖
cmd >> file  # file若非空，则追加写入

# stderr重定向
cmd 2> file
cmd 2> /dev/nul  # 将错误信息丢弃

# stdout与stderr全都重定向
cmd &> file  # 最简写法
cmd > file 2>&1  # 另一种写法
"""
错误写法
  cmd 2>&1 > file  # 如果先把2指向1，即屏幕，再指向file，只是1指向了file，2其实没变，还是指向屏幕
  cmd > file 2> file  # 这样虽然可以都保存但会交叉的混在一起
"""
```

- 用cat创建文件

```bash
cat  # 执行cat什么都不加则会让在命令行输入内容，ctrl+d退出
cat > file  # 回车后在命令行输入内容，退出则内容自动保存到file中

# 输入重定向，是0<的简写
# 原本由键盘输入的数据，改由文件内容来代替
cat > file1 < file2  # 从file2读取内容写入到file1中，类似于复制

# 设置输入结束符
cat > file << "eof"
```

## `export`

由于子进程仅会继承父进程的环境变量，所以需要将自定义变量转成环境变量才可以在任意进程中使用：`export x`(x前不需要加$)

比如想让自己写的可执行程序能在全局使用，需要把程序的路径添加到环境变量$PATH中

```bash
echo $PATH
"""
查看当前会在哪些路径搜索，冒号分隔，从左到右按顺序加载
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
"""
PATH=${PATH}:my_comd_path
echo $(export PATH)` >> ~/.bash_profile
source ~/.bash_profile  # 重新加载环境变量配置文件使其生效
```

## 运算

```bash
expr 1 + 1  # 2，运算符两边必须加空格
expr 5 / 2  # 2

"""
运算符
&& 和
|| 或
"""
```

## Shell Script

> 需要有x执行权限

指定解释器

- `#!/bin/bash`
- `#!/usr/bin/env bash`（推荐）

运行时会产生新的子进程：

- `bash ./filename.sh`（推荐，不用赋予执行权限）
- `./filename.sh`（必须声明，且要赋予执行权限）

运行时不会开启新进程，会影响当前shell，不用赋予执行权限：

- `source ./filename.sh`
- `. ./filename.sh`（.是source的简写）

## 管道 `｜`

进程通信的重要方式，把前一条命令的执行结果传给后一个命令，还会为两个命令创建子进程，由于内部命令在子进程中无法将结果传给父进程，所以不建议在管道中使用内部命令

比如常跟grep连用：`xxx | grep 参数 文件`