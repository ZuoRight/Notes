# 引言

Linux最初由Linus Torvalds基于Unix所编写，也就是说它是一个Unix-like系统，从[Unix timeline](https://commons.wikimedia.org/wiki/File:Unix_timeline.en.svg#/media/File:Unix_timeline.en.svg)或者[Unix history simple](https://zh.wikipedia.org/wiki/File:Unix_history-simple.svg)可以大概了解到它和Unix系统的一个关系

![20220707143751](http://image.zuoright.com/20220707143751.png)

## 内核

Linux本身只是一个开源的内核(Kernel)

> Kernel版本：<https://www.kernel.org/>

版本号：主版本号.次版本号(奇/偶数为开发/稳定版).末版本号

![20220707142531](http://image.zuoright.com/20220707142531.png)

```sh
# 查看内核信息
uname -a
"""
-s 查看系统内核(默认) Linux
-r 查看系统内核版本 5.4.0-26-generic
-m 查看系统硬件架构 x86_64
-a 查看所有信息

Linux localhost.localdomain 5.4.0-26-generic #30-Ubuntu SMP Mon Apr 20 16:58:30 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
"""

# 升级内核
yum/apt install kernel-xxx

# 更新已安装的其它软件包和补丁
yum/apt update
```

## 发行版

而我们平时常用的Linux其实指的是Linux发行版，它是在Kernel的基础上定制化扩展了很多服务和功能

俗话说，发行版本千千万，不是红帽就大便，意思就是Linux的发行版有很多，但主要分两个派系：Redhat和Debian

> 发行版列表：<https://zh.wikipedia.org/wiki/Linux%E5%8F%91%E8%A1%8C%E7%89%88>

![20220707151705](http://image.zuoright.com/20220707151705.png)

> 发行版选择器：<https://distrochooser.de/>

比较常见的是

- Ubuntu
- CentOS
- Alpine 容器中可能比较常用

## GNU工具集

- GCC/G++：编译环境
- glibc：C库
- coreutils：核心工具组

## 启动过程

1. `BIOS` 基本的输入输出系统，在主板上，在启动时按F2进入界面，选择引导介质
2. `MBR` 主引导记录部分，在硬盘上
3. `BootLoader` grub2.0 用于启动和引导内核的工具，即选择内核和指定版本
4. `Kernel` 内核
5. `systemd` 1号进程(以前是`init`)
6. `系统初始化`
7. `Shell` 用于解释用户对操作系统的操作

```bash
reboot  # 重启
shutdown  # 关机
```

## 终端

- 图形终端
- 字符终端
- 远程终端：SSH、VNC
- 代码终端：Fabric

Linux（桌面版）默认提供了七个终端（六个命令行界面以及一个图形界面，可以使用Alt+F1/.../7来切换），每个终端可以登录不同用户，每个用户每次登录后获取shell的PID都是不同的，据此可以进行一些个性化设置（`~/.bashrc`），由于CPU每秒可以在不同进程之间快速切换，所以多人同时使用系统时是无感知的。

> 如果某个终端因某个进程卡死导致界面不能动，可以切换到另一个终端，用`ps -aux`找出有问题的进程然后kill掉，再切回刚才的终端就又恢复了正常。

## 命令行

```bash
# 用户名@主机名:所在目录$/# 命令/可执行文件 选项 参数
root@localhost:~$ command [--options] parameter1 parameter2
<<"COMMENT"
系统管理员和普通用户的提示字符分别为：`#` 和 `$`  
选项名前通常用`-` 和 `--`表示简写和全写，也会有带+的情况  
指令间用至少一个空格来分隔，大小写敏感  
回车执行命令，`\`可将回车转义为换行输入  
执行多条命令，用分号隔开
COMMENT
```

修改主机名

```bash
sudo vim /etc/hostname
reboot  # 重启后生效
```

快捷键

- `Tab` 命令及选项补全/提示
- `Ctrl`+`c` 中断目前程序
- `Ctrl`+`d` 键盘输入结束EOF(End Of Input)
- `Shift`+`Page Up/Down` 向前/后翻看输出信息，`clear`后则翻不出来

查看默认shell

```bash
ls -l /bin/sh
<<"COMMENT"
/bin/sh -> dash
ubuntu默认shell是dash，性能高，但会导致一些bash命令失败
"""
# 切换成bash
sudo dpkg-reconfigure dash
"""
输入完密码，会弹出询问界面，问是否让dash为默认shell，选择否，将会删除dash，默认回到bash
COMMENT
```

时间与日期

```bash
# 查看时间
date -R
"""
Fri, 03 Feb 2019 00:57:49 +0800  # +东区，-西区，此处为东八区
"""
# 查看日历
cal 3 2019
```
