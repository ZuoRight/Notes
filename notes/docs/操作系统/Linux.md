# Linux

Linux是Unix-like系统，从Unix的timeline可以了解到它和其它Unix系统的发展历史以及大概关系，点击图片可以查看原图，更详细的可以查看：<https://zh.wikipedia.org/wiki/File:Unix_history-simple.svg>，此处不过多赘述。

<a href="https://commons.wikimedia.org/wiki/File:Unix_timeline.en.svg#/media/File:Unix_timeline.en.svg" target="_blank"><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/cd/Unix_timeline.en.svg/1200px-Unix_timeline.en.svg.png" alt="Unix timeline.en.svg"></a>

linux本身只是一个内核([Kernel](https://www.kernel.org/))，而我们平时使用的linux发行版在内核的基础上添加了GNU工具集和Shell（以终端或者图形界面的形式存在）等

- 发行版

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
- Shell 用于解释用户对操作系统的操作

## 命令行

`[root@localhost ~]$ command [--options] parameter1 parameter2`

[用户名@主机名 所在目录]提示字符 命令/可执行文件 选项 参数

> 系统管理员和普通用户的提示字符分别为：`#` 和 `$`  
> 选项名前通常用`-` 和 `--`表示简写和全写，也会有带+的情况  
> 指令间用至少一个空格来分隔，大小写敏感  
> 回车执行命令，`\`可将回车转义为换行输入  
> 执行多条命令，用分号隔开

## 升级

- Ubuntu

```bash
apt-get update  # 查看可更新软件列表
apt-get upgrade  # 软件版本升级

# 系统版本升级
apt-get dist-upgrade  # 方式之一
do-release-upgrade  # 官方推荐的方式（加参数-d可升级到最新开发版本）
```

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
