# Linux

Linux是Unix-like系统，从Unix的timeline可以了解到它和其它Unix系统的发展历史以及大概关系，点击图片可以查看原图，更详细的可以查看：<https://zh.wikipedia.org/wiki/File:Unix_history-simple.svg>，此处不过多赘述。

<a href="https://commons.wikimedia.org/wiki/File:Unix_timeline.en.svg#/media/File:Unix_timeline.en.svg" target="_blank"><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/cd/Unix_timeline.en.svg/1200px-Unix_timeline.en.svg.png" alt="Unix timeline.en.svg"></a>

linux本身只是一个内核([Kernel](https://www.kernel.org/))，而我们平时使用的linux发行版在内核的基础上添加了Shell和GNU工具集等

## 发行版

操作系统千千万，都是红帽和大便，意思就是Linux的发行版有很多，但主要分两个派系：Redhat和Debian

![20201222223417](http://image.zuoright.com/20201222223417.png)

## GNU工具集

- GCC/G++：编译环境
- glibc：C库
- coreutils：核心工具组

## 启动过程

- BIOS 基本的输入输出系统，在主板上，在启动时按F2进入界面，选择引导介质
- MBR 主引导记录部分，在硬盘上
- BootLoader grub2.0 用于启动和引导内核的工具，即选择内核和指定版本
- kernel 内核
- systemd/init 1号进程
- 系统初始化
- shell

## Shell

Shell是用户与内核之间的翻译官，即解释器，以终端或者界面的形式存在，用于解释用户对操作系统的操作。

shell有很多种

```shell
cat etc/shells
```

UNIX哲学：一条命令只做一件事。所以linux的shell脚本就是为了组合多个命令和多次执行

一般Linux系统默认的Shell都是bash。

提示符：`[root@localhost ~]`

在Linux系统中，一切都是文件，包括命令，也就是说所有的命令都是以文件的形式存在的，如果想要shell能直接解释，需要添加到PATH路径下

PATH路径有以下这些

```shell
[root@localhost ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```

如果想知道某个命令具体在哪个路径下，可以用`whereis xxx`来定位。

声明
命令
注释
赋予权限

二进制可执行文件，给x权限即可
脚本文件：要给可读可执行权限，创建文件时默认带了可读权限，所以只需要加可执行权限：`chmod u+x filename`

执行命令

运行时会产生新的子进程：
- bash ./filename.sh（推荐，可以不用赋予执行权限）
- ./filename.sh（必须声明，且要有权限）

运行时不会开启新进程，会影响当前shell，不用赋予执行权限：
source ./filename.sh
. ./filename.sh（.是source的简写）