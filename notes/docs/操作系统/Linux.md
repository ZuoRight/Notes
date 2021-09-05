# Linux

Linux是Unix-like系统，从Unix的timeline可以了解到它和其它Unix系统的发展历史以及大概关系，点击图片可以查看原图，更详细的可以查看：<https://zh.wikipedia.org/wiki/File:Unix_history-simple.svg>，此处不过多赘述。

<a href="https://commons.wikimedia.org/wiki/File:Unix_timeline.en.svg#/media/File:Unix_timeline.en.svg" target="_blank"><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/cd/Unix_timeline.en.svg/1200px-Unix_timeline.en.svg.png" alt="Unix timeline.en.svg"></a>

linux本身只是一个内核([Kernel](https://www.kernel.org/))，而我们平时使用的linux发行版在内核的基础上添加了GNU工具集和Shell（以终端或者图形界面的形式存在）等

```bash
uname [-a]
# Linux localhost.localdomain 5.4.0-26-generic #30-Ubuntu SMP Mon Apr 20 16:58:30 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
"""
-a 列出所有系统相关的信息
-s 只显示系统内核(默认) Linux
-r 系统内核版本 5.4.0-26-generic
-m 系统硬件架构 x86_64
"""
```

操作系统千千万，都是红帽和大便，意思就是Linux的发行版有很多，但主要分两个派系：Redhat和Debian

![20201222223417](http://image.zuoright.com/20201222223417.png)

## GNU工具集

- GCC/G++：编译环境
- glibc：C库
- coreutils：核心工具组

## 升级

- Ubuntu

```bash
apt-get update  # 查看可更新软件列表
apt-get upgrade  # 软件版本升级

# 系统版本升级
apt-get dist-upgrade  # 方式之一
do-release-upgrade  # 官方推荐的方式（加参数-d可升级到最新开发版本）
```

## 启动过程

- BIOS 基本的输入输出系统，在主板上，在启动时按F2进入界面，选择引导介质
- MBR 主引导记录部分，在硬盘上
- BootLoader grub2.0 用于启动和引导内核的工具，即选择内核和指定版本
- kernel 内核
- systemd/init 1号进程
- 系统初始化
- Shell 用于解释用户对操作系统的操作

## 命令行

`[root@localhost ~]$ command [--options] parameter1 parameter2`

[用户名@主机名 所在目录]提示字符 命令/可执行文件 选项 参数

> 系统管理员和普通用户的提示字符分别为：`#` 和 `$`  
> 选项名前通常用`-` 和 `--`表示简写和全写，也会有带+的情况  
> 指令间用至少一个空格来分隔，大小写敏感  
> 回车执行命令，`\`可将回车转义为换行输入  
> 执行多条命令，用分号隔开

Linux默认提供了七个终端（六个命令行界面以及一个图形界面，可以使用Alt+F1/.../7来切换），每个终端可以登录不同用户，每个用户每次登录后获取shell的PID都是不同的，据此可以进行一些个性化设置（`~/.bashrc`），由于CPU每秒可以在不同进程之间快速切换，所以多人同时使用系统时是无感知的

> 如果某个终端因某个进程卡死导致界面不能动，可以切换到另一个终端，用`ps -aux`找出有问题的进程然后kill掉，再切回刚才的终端就又恢复了正常。

## 账户相关

每个登录的用户都会获得两个ID：`UID`(User ID) 和 `GID`(Group ID)

![20210827231927](http://image.zuoright.com/20210827231927.png)

账号信息存储在`/etc/passwd`文件中，其中很多都是系统账号（系统运行所必须的），不可随意删除，早期密码也存储在这里，但后来因为安全原因单独存储到了`/etc/shadow`中

![20210827233831](http://image.zuoright.com/20210827233831.png)

如上图，密码为空则不需要密码即可登录，最近修改日期是从1(1970.1.1)开始累计的，多少天内不可修改或需要修改，为0则表示可任意修改，99999为273年，即不限制，如果想暂停某个用户登录，可以将失效日期改为0

```bash
# 创建用户
useradd chong [-m] -s /bin/bash  # -m强制建立家目录(一般默认)，-s指定Shell
# 修改账户信息
usermod -s /bin/bash  # 比如-s修改默认Shell
# 删除账户
userdel [-r] chong  # -r连同家目录一起删除
# 修改密码
passwd [username]  # 普通用户需要先输入旧密码，root用户不需要，root用户可以修改任意用户的密码且不需要输入旧密码，不加username则默认修改自己的
```

切换账户

```bash
# 方式1
# 需要输入对方密码（从root切换到其它用户不需要），使用exit可以退回原来的用户
su - [username]  # 不加username即切换到root用户，-建议一直带着

# 方式2
# 需要用权限才可以使用sudo，用visudo命令（使用vi仅有只读权限），在/etc/sudoers文件中给用户添加一行与root相同的权限即可
# 每次都需要输入自己的密码（root不需要），加-s可以保持session
sudo [-u username] command  # 不加-u即使用root用户，注意后面必须加命令一起使用
```

```bash
id [username]  # 查看某个用户的账号信息，不带用户名默认查看自己
groups  # 查看所属用户组
who 或者 w # 查看当前在线用户
write username  # 给某个在线用户发送消息
wall  # 给所有在线用户发送消息
mesg n/y  # 拒绝或允许接收消息，root用户默认拒绝，普通用户无法拒绝root用户发来的消息
mail -s "test content" [chonge@localhost]  # 发送邮件，也可以给离线用户发，给本机用户可以省略[chonge@localhost]，对方可以使用mail命令来接收查看邮件内容
```

![20210828001620](http://image.zuoright.com/20210828001620.png)

![20210828001628](http://image.zuoright.com/20210828001628.png)

## 快捷键

- `Ctrl` + `c` 中断目前程序
- `Ctrl` + `d` 键盘输入结束EOF(End Of Input)
- `Shift` + `Page Up/Down` 向前/后翻看输出信息，`clear`后则翻不出来
- `Tab` 命令及选项补全/提示

## 帮助

如果只记得命令或选项的开头部分，连续两个Tab键可列出所有可能项

### `command --help`

如果忘了某个命令的选项和参数可以用这个

### `man command`

查看用户手册(manual)，上下键滚动翻看man page，空格跳到下一页，`q`退出

查找关键字：`/string`(向下查找)，`?string`(向上查找)

- `man -f comd` 精准匹配所有comd命令相关的说明文件，简写：`whatis comd`
- `man -k comd` 模糊查询说明中包含有comd的说明文件，简写：`apropos comd`

![20210821144749](http://image.zuoright.com/20210821144749.png)

> 说明文件几个重要的类别：  
> 1 一般用户可使用的命令  
> 5 配置文件  
> 8 系统管理员可用的命令

- `man 7 man` 查看类别为7的man说明文件

### `info command`

与man差不多，是Linux独有的，更易读

### `usr/share/doc/...`

一些程序的安装须知等说明文件可能会放在这里
