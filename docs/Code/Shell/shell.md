# Bash

Shell 被称之为用户与内核交互的入口，常见的 Shell 有

- Bourne Shell（sh）
- Bourne Again shell（bash）
- C Shell（csh）
- TENEX C Shell（tcsh）
- Korn shell（ksh）
- Z Shell（zsh）
- Friendly Interactive Shell（fish）

```shell
# 查看系统安装的所有 Shell
cat /etc/shells
'
# List of acceptable shells for chpass(1).
# Ftpd will not allow users to connect who are not using
# one of these shells.

/bin/bash
/bin/csh
/bin/dash
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zsh
/opt/homebrew/bin/bash
'
```

最常用的 Shell 当属 Bash，它是 GNU 社区对 Bourne Shell 的重写，使其符合自由软件协议，其它 Shell 基本都兼容 Bash

```shell
# 查看版本
bash --version
# 或者
echo $BASH_VERSION

bash  # 如果命令行默认的不是bash，可以输入bash进入

exit  # 退出
```

[Bash 免费教程](https://wangdoc.com/bash/intro.html)

## 默认 Shell

Ubuntu 默认 Shell 是 dash，性能高，但会导致一些 bash 命令失败

默认 shell 不一定是当前正在使用的 shell，使用 `ps` 命令返回进程列表，倒数第二个一般是当前正在使用的 shell

```shell
# 查看默认 Shell
echo $SHELL

# 切换 Shell
# 需要输入密码，会弹出询问界面，问是否让 dash 为默认 shell，选择否，将会删除 dash，默认回到 bash
sudo dpkg-reconfigure dash
```

macOS 从 Catalina 版开始默认 Shell 为 zsh

- `.zprofile` 用于登录时的初始化，适合设置与系统环境相关的内容
- `.zshrc` 用于交互式 shell 的配置，适合设置动态的、与交互相关的内容（如别名、函数、动态环境切换等）

### 设置 MacOS 默认 Shell

> 参考译文：<https://juejin.im/post/6844903972294262791>

可以将默认 shell 切换为 bash

但由于 GPLv3 的原因，Mac 自带的 bash 还是十几年前的 3.2 版本，所以需要先升级一下，即下载新版 bash 并设为默认

> tips：编写 shell 脚本如果想用新 bash 解释，需要把 `#!/bin/bash` 改为 `#!/usr/local/bin/bash`

```shell
bash --version  # 查看当前交互shell版本
echo $BASH_VERSION  # 查看默认交互shell版本

# 安装新版
brew install bash

# 查看 bash 路径
which -a bash
# 新版：/usr/local/bin/bash
# 旧版：/bin/bash，为了保证系统的完整性，不建议删除

# 设置交互shell为新版
chsh -s /usr/local/bin/bash
# 改回旧版 bash：chsh -s /bin/bash
# 改回 zsh：chsh -s /bin/zsh

# 设置登录 shell 为新版
# 即把 /usr/local/bin/bash 填加到当前系统受信任 shells 配置文件中，
sudo vim /etc/shells  # 需要管理者权限
```

## 命令类型

Shell CLI（Command Line Interface）是命令行交互环境，在其中执行的命令可分为

- 内建命令：Shell自带的命令，可直接执行
- 外部命令：调用外部可执行文件，存放在 `PATH` 对应的路径中，比如 `/bin` 或 `/usr/bin`
- 自定义脚本

类似于 Python，Shell 其实是一个编程环境，无论哪种命令实际上都是在执行一段 Shell 命令解释器可以解释的脚本（Script）

```shell
# -t 参数返回命令类型
echo -t pwd  # builtin
echo -t ls  # file
echo -t if  # keyword

# 返回所有类型属性
type -a echo  # echo 既是内置命令也有对应的外部程序
'
echo is shell builtin
echo is /usr/bin/echo
echo is /bin/echo
'

type ls  # ls is hashed (/bin/ls)
'
ls 是一个外部命令，第一次执行时会去 $PATH 指定的路径搜索命令对应的可执行文件
然后会把命令的路径缓存在哈希表中，下一次调用时就不直接去这个路径调用

hash -l 列出哈希表中所有条目
hash -r 清空哈希表
hash -d xxx 删除指定命令的哈希条目
'
```

环境变量 `PATH`，是一个包含多个目录路径的字符串，它告诉 Shell 命令解释器去哪里查找可执行文件。

```shell
echo $PATH  # 查看 PATH 包含的路径
'
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin  # 冒号分割，从左到右按顺序加载
'

which pwd  # 查找可执行文件的 PATH 路径
whereis xxx  # 可以查找不在 PATH 路径的可执行文件
```

通常我们自己安装软件或工具时，可执行文件都会被放入以上位置，如果不在则需要自己将路径增加到 `PATH` 变量中，否则无法全局使用

```shell
echo 'export PATH=$PATH:/path/to/new/folder' >> ~/.bash_profile  # 新增，也可以直接编辑这个文件
source ~/.bash_profile  # 重新加载配置文件使其生效
```

## Shell CLI

### 命令行提示符

默认格式：`用户名@主机名:所在目录 身份`，也可以自定义格式

```shell
root@localhost:~$
# ~ 表示当前用户的主目录
# $ 代表当前为普通用户身份，如果是系统管理员则显示 # 号
```

```shell
# 查看主机名
hostname

# 修改主机名
sudo vim /etc/hostname
reboot  # 重启后生效
```

### 命令格式

```shell
command [--options] [arg1 arg2 ... argN]
'
参数间用至少一个空格来分隔，大小写敏感。
参数前可用 - 或 -- 添加选项，有些命令的选项可以不加 -，也有使用 + 的情况。
回车执行命令，命令较长时，可使用反斜杠 \ 将回车转义为换行输入。
'

# 可以将较长的命令串设置一个较短的别名
alias cip='ping cip.cc'

# &加在命令末尾表示后台运行
xxx &
```

批处理，即同时执行多个命令

```shell
# 命令结束符;分号，先执行分号前的命令，无论成功与否都会执行分号后面的命令
mkdir ./demo/; mv abc.txt ./demo/

# 命令组合符
# && 逻辑与：如果前面的命令失败了，则不再执行后面的命令
# || 逻辑或：如果前面的命令失败了，则尝试执行后面的命令
mkdir ./xxx/ && mv abc.txt ./xxx/ || echo "FAILED!!!"
```

### 快捷键

```text
Tab 命令及选项补全/提示
Shift + PageUp/PageDown 向前/后翻看输出信息，清空后则翻不出来

Ctrl + s 打开/关闭回显（即输入不显示在屏幕上，常见于输入密码时）

Ctrl + c 中断目前程序
Ctrl + d 键盘输入结束EOF(End Of Input)，即关闭shell会话

Ctrl + z 让进程变为后台进程，用 fg 命令切换回前台

Ctrl + w 删除光标前一个单词
Ctrl + u 删除光标前的单词直到行首
Ctrl + k 删除光标后的单词直到行尾
```

## Shell Script

<https://wangdoc.com/bash/script>

### 指定解释器

- `#!/bin/bash`
- `#!/usr/bin/env bash`（推荐）

运行时不会开启新进程，会影响当前 Shell

- `source ./filename.sh`
- `. ./filename.sh`（ `.` 是 source 的简写）

执行外部命令时，Shell 会创建一个子进程来启动可执行文件

- `bash ./filename.sh`（推荐，不用赋予x执行权限）
- `./filename.sh`（必须声明，且要赋予执行权限）

### 输出

- 输出文本

```shell
echo hello world

# 如果输出多行文本需要放在引号中
echo "<HTML>
    <BODY>
          Page body.
    </BODY>
</HTML>"

# 无论单引号还是双引号，引号中的特殊字符都会原样输出（不过$符号在双引号中会视作引用变量），使用-e可以解释为特殊字符
echo -e "Hello\nWorld"

# 输出文本末尾会有一个回车符
echo -n test  # 使用 -n 参数取消末尾回车
```

- 输出变量

```shell
echo $y
echo ${#x}  # 获取变量长度
echo ${x:n:m}  # 切片
```

- eval

eval 语句告诉 shell，将 eval 的参数作为命令，并通过命令行运行它们

```shell
foo=10
x=foo
y='$'$x  # echo y=$foo
eval y='$'$x  # echo y=10
```

### 运算

```shell
expr 1 + 1  # 2，运算符两边必须加空格
expr 5 / 2  # 2
```

## 变量

```shell
set  # 查看所有变量，特别长
set | grep PATH  # 过滤

env  # 查看所有环境变量

# 删除变量
unset var_name  # 变量名不需要加$
```

### 预定义变量

预定义变量是 Shell 或操作系统定义的变量，这些变量通常提供关于系统环境或运行时环境的重要信息，在登录时由系统初始化

- 环境变量

```shell
$SHELL  # 查看默认Shell
$PATH  # 可执行文件搜索路径

$USER  # 当前登录用户
$UID  # 当前用户UID
$HOME  # 用户的家目录
```

- 特殊变量

由 Shell 内部使用的特殊参数或状态变量

这些变量特定于当前的 Shell 会话，不是环境变量。

```shell
$PS1  # Prompt Statement One，控制命令行提示符内容

$?  # 显示上一条命令结果是否正确，正确返回0，否则返回1

$$  # 显示当前shell的PID，脚本检测时会用到
$0  # 当前进程的名称（脚本或命令的完整路径）

# 位置参数
$1  # 脚本或命令的第一个参数
${2-_}  # 如果没有传入位置参数，默认赋值下划线
```

### 自定义变量

```shell
# 变量赋值，无需声明
'
赋值符左右不能有空格，有空格时需要使用引号括起来
还可以用反斜杠转义特殊字符：x0=12\ 4）
'
var=1234
var='hello world'  # 单引号中内容全部视作为纯文本，包括$符号
var="$x1"  # 双引号中的$可以视作引用变量

# 引用变量
y=${x}  # 可省略花括号
y=$(ls -al)  # 引用命令（推荐）
y=`ls -al`  # 引用命令
```

设置环境变量要用 `export`

```shell
# 临时添加
export ENVVAR="I am an environment variable"

# 从命令行追加到环境变量文件，比如 ~/.bash_profile
echo 'export PATH="$PATH:/usr/local/bin"' >> ~/.bash_profile

# 或者编辑 .bash_profile 等文件手动添加
# 扩增 $PATH 变量，冒号作为分隔符，$PATH 也可以放在冒号前或冒号后
PATH=$PATH:/home/bin
PATH="$PATH":/home/bin
PATH=${PATH}:/home/bin  # 推荐
```

设置环境变量后需要重启终端，或者重新加载配置文件：`source ~/.bash_profile` 或 `source ~/.zprofile`

## 数组

```shell
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

## 通配符扩展

Shell 接收到用户输入的命令以后，会根据空格将用户的输入，拆分成一个个词元（token）。然后扩展词元里面的特殊字符，扩展完成后才会调用相应的命令。

扩展可以看作是原始的正则表达式

- `~`

```shell
echo ~  # 扩展成当前用户的主目录
echo ~user_xx  # 用户 user_xx 的主目录，比如 ～root
echo ~/dir  # 主目录的某个子目录
echo ~+  # 等价于 pwd
```

- `?` 匹配单个字符，不包括空字符

```shell
ls ?.txt  # a.txt b.txt
ls ??.txt  # ab.txt
```

- `*` 匹配任意数量字符，包括0个

```shell
ls *  # 返回当前目录所有文件，但不包括隐藏文件，也不包括子目录
ls .*  # 返回当前目录所有隐藏文件，排除 . 和 .. 可以写成 .[!.]*

ls a*.txt  # a.txt ab.txt
ls *.txt  # a.txt b.txt ab.txt
ls **/*.txt  # 匹配当前目录以及任意深度子目录中的 txt 文件
```

- `[xyz]` 匹配 xyz 中任一字符

```shell
ls [ab].txt  # a.txt b.txt
ls [ab-]  # 如果要匹配连字符-只能放到开头或结尾

# 匹配不在方括号中的字符
ls [!ab].txt  # c.txt
ls [^ab].txt  # c.txt

'
[abc]* 以a、b、c字符之一开头的所有文件名
program.[co] 文件 program.c 与文件 program.o
'
```

- `[x-z]` 匹配一个连续的范围

```shell
ls [a-c].txt  # abc.txt
ls test[1-3].txt  # test1.txt test2.txt test3.txt
ls test[!1-3].txt  # 不属于这个范围的 test6.txt

'
[a-z] 所有小写字母
[a-zA-Z] 所有字母，包含小写和大写
[a-zA-Z0-9] 所有字母与数字
BACKUP.[0-9][0-9][0-9] 所有以BACKUP.开头，后面是三个数字的文件名
'
```

- `{x,y,z}` 匹配大括号中所有值

```shell
ls test{1,2,3}.txt  # test1.txt test2.txt test3.txt

# 逗号前可以没有值，但前后不能有空格
cp a.log{,.bak}  # 等价于 cp a.log a.log.bak

# 可以嵌套
echo {j{p,pe}g,png}  # jpg jpeg png
```

- `{start..end}` 扩展为连续序列，支持倒序、步长、笛卡尔积

```shell
echo {1..4}  # 1 2 3 4
echo {c..a}  # c b a
echo .{mp{3..4},m4{a,b,p,v}}  # .mp3 .mp4 .m4a .m4b .m4p .m4v
echo {0..8..2}  # 0 2 4 6 8
echo {a..c}{1..3}  # a1 a2 a3 b1 b2 b3 c1 c2 c3
```

## 数据流

数据流，可类比为水管，用于传输数据

POSIX 系统中，每个进程都内置了三个标准数据流

- 标准输入流：`stdin`
- 标准输出流：`stdout`
- 标准错误流：`stderr`

当进程启动后，stdin 默认对接到终端的输入，stdout & stderr 默认对接到终端的输出

![20230401234228](https://image.zuoright.com/20230401234228.png)

在程序中打开某个文件，会得到一个文件描述符 `fd`(file descriptor)，可以通过 fd 对文件进行读写，进程的标准流相当于三个特殊的 fd，在操作系统启动时就已经准备好了，分别对应 0、1、2 三个描述符

### stdin 输入重定向

输入重定向是指将一个命令的输入从键盘改为来自文件或另一个命令的输出

- `<` 将一个文件的内容作为命令的输入
- `<<` 允许用户从终端输入多行文本，直到输入一个特定的分界符为止
- `<<<` 将一个字符串作为命令的输入

```shell
command < input_file
wc -l < /etc/xxx  # 统计输入行数

# 直到输入 EOF 为止，并将这些文本作为 sort 命令的输入进行排序
sort << EOF
line 3
line 1
line 2
EOF

# 将字符串 "hello world" 作为 grep 命令的输入，并在其中查找单词 "world"
grep world <<< "hello world"
```

### stdout 输出重定向

输出重定向是指将命令的输出从终端改为写入到文件或作为另一个命令的输入

- `>` 表示覆盖
- `>>` 表示追加

```shell
ls > filelist.txt  # 覆盖写入
date >> logfile.txt  # 追加写入

# 执行cat，命令行等待输入，Ctrl+d退出时输入内容自动保存到file
cat > file

# 从file1读取内容写入到file2
# 类似于复制（仅仅复制了内容，但不能复制属性，所以无法替代cp）
cat < file1 > file2
cat > file2 < file1

# 命令行输入多行，遇到EOF结束输入，内容保存到file
cat > file << EOF
```

### stderr 错误重定向

- `2>`

```shell
cmd 2> file

# 将错误信息丢弃
cmd 2> /dev/nul  # /dev/null是一个特殊文件，所有传给它的东西它都丢弃掉
```

### stderr 重定向到 stdout

- `2>&1`

```shell
ls /path/to/nonexistent 2>&1
```

### stderr 和 stdout 一起重定向到文件

- `&>` 首选写法
- `>&` 老式写法

```shell
ls /path/to/nonexistent &> output.txt

# 另一种写法
cmd > file 2>&1  # 1重定向到file，2重定向到1，最终一起重定向
cmd > /dev/null 2>&1  # 等价于 cmd &>，将所有输出丢弃

# 错误写法
cmd 2>&1 > file  # 如果先把2重定向到1，只有1会被重定向到file
cmd >> file 2>> file  # 这样虽然可以都保存但会交叉的混在一起
```
