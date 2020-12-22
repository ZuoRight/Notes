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

## Shell

Shell是用户与内核之间的翻译官，以终端或者界面的形式存在，Shell可以把用户输入的命令解释为操作系统内核可执行的指令。

bash是Linux系统默认的Shell。

提示符：`[root@localhost ~]`

在Linux系统中，一切都是文件，包括命令，也就是说所有的命令都是以文件的形式存在的，如果想要shell能直接解释，需要添加到PATH路径下

PATH路径有以下这些

```shell
[root@localhost ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```

如果想知道某个命令具体在哪个路径下，可以用`whereis xxx`来定位。
