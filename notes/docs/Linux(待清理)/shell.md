# shell

shell脚本为了组合多个命令和多次执行（UNIX哲学：一条命令只做一件事）

- 查看默认shell：`echo $SHELL`
- 查看当前系统受信任shell：`cat /etc/shells`

## 编辑脚本

- vi
- vim

## 指定解释器

- `#!/bin/bash`
- `#!/usr/bin/env bash`（推荐）

## 变量

- 变量赋值

```bash
# 无需声明，等号左右不能有空格
x=1
# 右边有空格时使用引号
x="a b"
# 将命令结果赋值给变量
x=$(ls -l /etc)
x=`ls -l /etc`
```

---

- 变量引用：`${变量名}`（花括号可省略：`$变量名`）
- 变量输出：`echo $x`
- 变量导出：`export x`（子进程可以获取到父进程的变量）
- 变量删除：`unset x`

---

- 其它操作

```bash
# 获取字符串长度
echo ${#x}

# 切片
echo ${x:n:m}

# 查看环境变量、预定义变量、位置变量
set | more

# 只查看环境变量
env | more
```

### 常用预定义变量

```bash
# 显示上一条命令结果是否正确
echo $?

# 显示当前进程pid，脚本检测时会用到
echo $$

# 显示当前进程的名称
echo $0
```

### 常用位置变量

```bash
echo $1

# 如果没有传入位置参数，默认赋值下划线
echo ${2-_}
```

### 常用环境变量

```bash
# 当前用户
echo $USER

# 当前用户uid
echo $UID

# 命令搜索路径：`/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin`
echo $PATH

# 可以修改提示符`[root@localhost ~]`的内容
echo $PS1
```

## 环境变量配置文件

/etc/路径的配置文件是所有用户通用的，~/下是特有用户的

- Linux

```bash
# 按加载顺序依次为
/etc/profile
/etc/profile.d/
~/.bash_profile
~/.bashrc
/etc/bashrc
```

- Mac

```bash
# 按加载顺序依次为
/etc/profile
/etc/paths
~/.bash_profile  # 有这个文件后面的就不读了
~/.bash_login
~/.profile
~/.bashrc
```

### 修改环境变量-$PATH

在Linux系统中，一切都是文件，包括命令，也就是说所有的命令都是以文件的形式存在的

如果想能被shell直接解释，需要把路径添加到环境变量$PATH中

- 查看：`echo $PATH`
- 新增：`echo 'export PATH=$PATH:xxx' >> ~/.bash_profile`
- 生效：`source ~/.bash_profile`

## 运行脚本

运行时会产生新的子进程：
- `bash ./filename.sh`（推荐，不用赋予执行权限）
- `./filename.sh`（必须声明，且要赋予执行权限）

运行时不会开启新进程，会影响当前shell，不用赋予执行权限：
- `source ./filename.sh`
- `. ./filename.sh`（.是source的简写）
