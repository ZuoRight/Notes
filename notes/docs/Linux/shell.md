# shell 编程

## 创建脚本

vim

## 注释

```bash
# 单行注释

:<<EOF
多行注释
多行注释
EOF
```

## 指定解释器

- 方式1: `#!/bin/bash`
- 方式2: `#!/usr/bin/env bash`（推荐）


## 运行脚本

脚本中未指定解释器时

- `bash /path/to/script.sh`
- `source /path/to/script.sh`

脚本指定了解释器可以直接运行

- `./path/to/script.sh`

## 变量

shell没有数据类型，所以无需声明，直接赋值即可

```bash
x = 1
x = a
x = 'a'
x = "a"  # 建议使用双引号，单引号内不能使用转义字符
```

```bash
# 只读变量，不能被改变，也不能被删除
readonly x

# 环境变量
export x
# 常见的环境/系统变量
$HOME 当前用户的用户目录
$PATH 用分号分隔的目录列表，shell 会到这些目录中查找命令
$PWD 当前工作目录
$RANDOM 0 到 32767 之间的整数
$UID 数值类型，当前用户的用户 ID
$PS1 主要系统输入提示符
$PS2 次要系统输入提示符
```

```bash
# 输出变量，花括号可省略，但建议使用
echo ${x}
# 获取字符串长度
echo ${#x}
# 切片
echo ${x:n:m}
# 删除变量
unset x
```

## 数组

```bash
x = (a, b, c)
# 访问数组
echo ${x[0]}
# 访问数组的所有元素
echo ${x[*]}
echo ${x[@]}
# 获取数组长度
echo ${#x[*]}
# 删除数组中元素
unset x[0]
```

## 运算符

AND: `&&`

OR: `｜｜`