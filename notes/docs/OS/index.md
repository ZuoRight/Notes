# 引言

Linux 是一个 Unix-like 系统，最初由 Linus Torvalds 基于 Unix 所编写

- [Unix timeline](https://commons.wikimedia.org/wiki/File:Unix_timeline.en.svg#/media/File:Unix_timeline.en.svg)
- [Unix history simple](https://zh.wikipedia.org/wiki/File:Unix_history-simple.svg)

![20220707143751](http://image.zuoright.com/20220707143751.png)

## 内核 Kernel

Linux 本身只是一个开源的内核(Kernel)

`uname -a`

![20230401113035](http://image.zuoright.com/20230401113035.png)

内核版本：主版本号.次版本号.末版本号

> 次版本号，奇数为开发版，偶数为稳定版  
> <https://www.kernel.org/>

![20220707142531](http://image.zuoright.com/20220707142531.png)

## 系统启动过程

1. `BIOS` 基本的输入输出系统，在主板上，在启动时按F2进入界面，选择引导介质
2. `MBR` 主引导记录部分，在硬盘上
3. `BootLoader` grub2.0 用于启动和引导内核的工具，即选择内核和指定版本
4. `Kernel` 内核
5. `系统初始化`
6. `init` 或 `systemd` 1号进程
7. `Shell`

一个 Linux 操作系统，在系统打开电源，执行 BIOS/boot-loader 之后，就会由 boot-loader 负责加载 Linux 内核

> 内核执行文件一般会放在 `/boot` 目录下，文件名类似 `vmlinuz*`

在内核完成了操作系统的各种初始化之后，会从缺省路径尝试启动第一个用户态进程，即1号进程，也叫初始化守护进程，然后操作系统便从内核态切换到了用户态。

当 Shell 启动了某个进程，会调用某个系统API（e.g. `tcsetpgrp`）把进程编号PID与Shell所属的终端关联起来，当终端需要发送信号时，会调用某个系统API（`tcgetpgrp`）获取进程的编号
