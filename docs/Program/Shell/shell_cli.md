# Shell CLI

[Bash 免费教程](https://wangdoc.com/bash/intro.html)

```shell
# 查看版本
bash --version
# 或者
echo $BASH_VERSION

# 如果命令行默认的不是bash，可以输入bash进入
bash
# 退出
exit
```

## 命令行提示符

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

## 命令格式

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

## 批处理

即同时执行多个命令

```shell
# 命令结束符;分号，先执行分号前的命令，无论成功与否都会执行分号后面的命令
mkdir ./demo/; mv abc.txt ./demo/

# 命令组合符
# && 逻辑与：如果前面的命令失败了，则不再执行后面的命令
# || 逻辑或：如果前面的命令失败了，则尝试执行后面的命令
mkdir ./xxx/ && mv abc.txt ./xxx/ || echo "FAILED!!!"
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

## 常用命令

- 内置命令：Shell 自带的命令
- 外部程序：实际对应的是某个可执行文件，可以被 `whereis xxx` 查找到的命令都属于外部命令，执行外部命令时，Shell 会创建一个子进程来启动可执行文件

### type

查看命令的来源，以此区分内置命令或外部程序

```shell
type echo
'
echo is a shell builtin
'

type ls
'
ls is hashed (/bin/ls)
'

# -a 参数查看命令的所有定义
type -a echo  # echo既是内置命令也有对应的外部程序
'
echo is shell builtin
echo is /usr/bin/echo
echo is /bin/echo
'

# -t 参数返回命令类型
echo -t pwd  # builtin
echo -t ls  # file
echo -t if  # keyword
```

### pwd

显示当前所在目录

```shell
pwd
```

## 输出

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

# 输出文本末尾会有一个回车符，可使用-n参数取消末尾回车
echo -n test
```

- 输出变量

```shell
echo $y
echo ${#x}  # 获取变量长度
echo ${x:n:m}  # 切片
```

常用预定义变量(一般都大写)

```shell
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

## 变量

- 查看所有变量

```shell
set  # 本地Shell变量
env  # 查看环境变量
```

```shell
# 变量赋值，无需声明
x=1234  # 赋值符=左右不能有空格，值有空格时需要使用引号括起来（还可以用反斜杠转义特殊字符：x0=12\ 4）
x1='hello world'  # 单引号中内容全部视作为纯文本，包括$符号
x2="$x1"  # 双引号中的$可以视作引用变量

# 取消变量
unset x  # x前不需要加$

# 引用变量
y=${x}  # 可省略花括号
y=$(ls -al)  # 引用命令（推荐）
y=`ls -al`  # 引用命令

# 扩增变量
PATH=$PATH:/home/bin
PATH="$PATH":/home/bin
PATH=${PATH}:/home/bin  # 推荐
```

- `$PATH`

环境变量PATH，是一个包含多个目录路径的字符串，它告诉Shell命令解释器去哪里查找可执行文件。

使用 `echo $PATH` 查看它包含的路径：`/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin`（冒号分割，从左到右按顺序加载）

通常我们自己安装软件或工具时，可执行文件都会被放入以上位置，如果不在则需要自己配置到PATH中，否则无法全局使用

```shell
echo 'export PATH=$PATH:/path/to/new/folder' >> ~/.bash_profile  # 新增，也可以直接编辑这个文件
source ~/.bash_profile  # 重新加载配置文件使其生效
```

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

## 运算

```shell
expr 1 + 1  # 2，运算符两边必须加空格
expr 5 / 2  # 2
