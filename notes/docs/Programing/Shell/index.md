# 引言

终端指基于文本的输入输出机制，但它并不理解具体的命令及其语法，需要引入Shell来解释，所以Shell被称之为用户与内核交互的入口

## Terminal 终端

> UART(Universal Asynchronous Receiver and Transmitter) 用来处理物理线路的字符传输（比如错误校验和流控等）  
> LDISC(line discipline) 用来撮合底层的硬件驱动与上层的系统调用，并完成某些控制字符的处理与翻译

如上图，终端的发展顺序为：硬件终端（蓝色路径） -> 终端模拟器（橙色路径） - 伪终端（绿色路径）

在计算机早期还是大型机的时代，操作系统由单任务发展为多任务的分时系统(time-sharing)，允许多人同时使用一台机器，于是便引入了终端(terminal)的概念

由系统管理员控制的本地终端被称之为控制台(Console)，由电话网络连入的其它终端被称之为远程终端

![20230330233936](http://image.zuoright.com/20230330233936.png)

最初的终端就是指电传打字机(Teletype)，打印机作为输入，打印纸作为输出，后来使用CRT显示器替代了打印纸

> tty是电传打印机英文单词的缩写，所以通常用tty表示终端的统称  
> 早期连接终端设备是通过串行端口连接的，所以串口设备也常被称呼为tty设备

![Teletype Model 33 ASR](http://image.zuoright.com/20230330233916.png)

![VT05 终端](http://image.zuoright.com/20230330233924.png)

随着计算机的不断发展，硬件终端变为了现在的终端模拟器(Terminal Emulator)，可分为由内核直接提供的 Virtual Console，和图形化终端模拟器 Terminal Window 两种

Linux 内置了7个虚拟终端，的前 6 个为字符终端(Character Terminal)，用于命令行交互(CLI) ，第 7 个预留给了图形终端(Graphical Terminal)，用于GUI操作，可以使用快捷键 `[Ctrl] + Alt + F1/2/3/4/5/6/7` 来切换，每个终端可以登录不同用户，每个用户每次登录后获取Shell的PID都是不同的，据此可以进行一些个性化设置（`~/.bashrc`），由于CPU每秒可以在不同进程之间快速切换，所以多人同时使用系统时是无感知的

> 如果某个终端因某个进程卡死导致界面不能动，可以切换到另一个终端，用`ps -aux`找出有问题的进程然后kill掉，再切回刚才的终端就又恢复了正常

我们现在连接远程服务器所使用的SSH、VNC、Fabric等客户端/软件库通常被称之为远程终端

## Shell

Shell 有以下几种释义

- CLI(Command Line Interface) 命令行交互，通常指的是这个
- Script 命令解释器，即脚本
- 工具箱，提供各种小工具方便使用操作系统的功能

常见的 Shell 有

- Bourne Shell（sh）
- Bourne Again shell（bash）
- C Shell（csh）
- TENEX C Shell（tcsh）
- Korn shell（ksh）
- Z Shell（zsh）
- Friendly Interactive Shell（fish）

最常用的 Shell 当属 Bash，它是 GNU 社区对 Bourne shell 的重写，使其符合自由软件协议，其它 Shell 基本都兼容 Bash

### 查看默认shell

```shell
# Ubuntu 默认 Shell 是 dash，性能高，但会导致一些 bash 命令失败
# MacOS 默认 Shell 是 zsh
ls -l /bin/sh
# 也可以用
echo $SHELL

# 查看内置shell
cat /etc/shells

# 切换shell
# 需要输入密码，会弹出询问界面，问是否让dash为默认shell，选择否，将会删除dash，默认回到bash
sudo dpkg-reconfigure dash
```

### 设置默认SHELL

> 参考译文：<https://juejin.im/post/6844903972294262791>

Mac 系统从 macOS Catalina 版开始默认 Shell 为 zsh，可以切换为bash

但由于GPLv3的原因，Mac自带的 bash 还是十几年前的 3.2 版本，所以需要先升级一下，即下载新版 bash 并设为默认

> tips：编写shell脚本如果想用新bash解释，需要把 `#!/bin/bash` 改为 `#!/usr/local/bin/bash`  

```shell
bash --version  # 查看当前交互shell版本
echo $BASH_VERSION  # 查看默认交互shell版本

# 安装新版
brew install bash

# 查看bash路径
which -a bash
# 新版：/usr/local/bin/bash
# 旧版：/bin/bash，为了保证系统的完整性，不建议删除

# 设置交互shell为新版
chsh -s /usr/local/bin/bash
# 改回旧版bash：chsh -s /bin/bash
# 改回zsh：chsh -s /bin/zsh

# 设置登录shell为新版
# 即把/usr/local/bin/bash填加到当前系统受信任shells配置文件中，
sudo vim /etc/shells  # 需要管理者权限
```

## 工具箱

```shell
# 查看时间
date -R  # Fri, 03 Feb 2019 00:57:49 +0800（注释：+东区，-西区，所以此处为东八区）

# 查看日历
cal 3 2019
```

## Reference

- <https://program-think.blogspot.com/2019/11/POSIX-TUI-from-TTY-to-Shell-Programming.html>
- [Bash免费教程](https://wangdoc.com/bash/intro.html)
