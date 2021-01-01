# Linux

Linux是Unix-like系统，从Unix的timeline可以了解到它和其它Unix系统的发展历史以及大概关系，点击图片可以查看原图，更详细的可以查看：<https://zh.wikipedia.org/wiki/File:Unix_history-simple.svg>，此处不过多赘述。

<a href="https://commons.wikimedia.org/wiki/File:Unix_timeline.en.svg#/media/File:Unix_timeline.en.svg" target="_blank"><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/cd/Unix_timeline.en.svg/1200px-Unix_timeline.en.svg.png" alt="Unix timeline.en.svg"></a>

linux本身只是一个内核([Kernel](https://www.kernel.org/))，而我们平时使用的linux发行版在内核的基础上添加了GNU工具集和Shell（以终端或者图形界面的形式存在）等

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
- shell 用于解释用户对操作系统的操作

### 赋予执行权限

- 创建文件时默认带了读写权限，查看权限：`ls -l`
- 加执行权限：`chmod u+x filename`

### 命令

执行多条命令，用分号隔开