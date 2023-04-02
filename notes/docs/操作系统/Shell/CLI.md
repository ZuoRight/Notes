# CLI

[Bash免费教程](https://wangdoc.com/bash/intro.html)

## 命令行提示符

```shell
# 可以自定义，但通常为如下格式
root@localhost:~$
# 用户名@主机名
# 所在目录
# $代表当前为普通用户身份，#代表系统管理员
```

当连接多个远程终端时，使用不同主机名方便区分

```shell
sudo vim /etc/hostname  # 修改主机名
reboot  # 重启后生效
```

## 命令格式

```shell
command [--options] parameter1 parameter2
# 选项名前通常用【-】和【--】表示简写和全写，也会有带+的情况
# 参数间用至少一个空格来分隔，大小写敏感
# 回车执行命令，命令较长时，可使用【\】将回车转义为换行输入
# 执行多条命令，可以用分号隔开

# 可以将较长的命令串设置一个较短的别名
alias cip='ping cip.cc'

# 特殊字符
~ 表示当前用户的主目录

# 通配符
? 匹配一个字符
* 匹配多个字符，复制所有文件
[xyz] 匹配xyz中任一字符
[a-z] 匹配一个范围
[!xyz]或[^xyz] 不匹配

# &加在命令末尾表示后台运行
xxx &
```

- 内部命令：内置在shell中的命令
- 外部命令：实际对应的是某个可执行文件

可以被 `whereis xxx` 查找到的命令都属于外部命令

执行外部命令时，Shell 会创建一个子进程来启动可执行文件，内部命令则不会

## `$PATH`

环境变量PATH，是一个包含多个目录路径的字符串，它告诉Shell命令解释器去哪里查找可执行文件。

使用 `echo $PATH` 查看它包含的路径：`/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin`（冒号分割，从左到右按顺序加载）

通常我们自己安装软件或工具时，可执行文件都会被放入以上位置，如果不在则需要自己配置到PATH中，否则无法全局使用

```shell
echo 'export PATH=$PATH:/path/to/new/folder' >> ~/.bash_profile  # 新增，也可以直接编辑这个文件
source ~/.bash_profile  # 重新加载配置文件使其生效
```

## 变量

```shell
# 变量赋值，无需声明
x=123  # 赋值符=左右不能有空格
# 值有空格时可使用引号括起来（还可以用反斜杠转义特殊字符：x0=ha\ ha）
x1='hello world'  # 单引号中内容全部视作为纯文本
x2="$x1"  # 双引号中的特殊字符保持原义，比如$

# 取消变量
unset x  # x前不需要加$

# 查看所有变量
set

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

## 输出

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

## 运算

```shell
expr 1 + 1  # 2，运算符两边必须加空格
expr 5 / 2  # 2

"""
运算符
&& 和
|| 或
"""
```

## 批处理

即同时执行多个命令

```shell
mkdir ./demo/; mv abc.txt ./demo/  # 先执行分号前的命令，无论成功与否都会执行分号后面的命令
mkdir ./xxx/ && mv abc.txt ./xxx/ || echo "FAILED!!!"
# && 逻辑与：如果前面的命令失败了，则不再执行后面的命令
# || 逻辑或：如果前面的命令失败了，则尝试执行后面的命令
```
