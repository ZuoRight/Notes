# 帮助

Linux 的命令有很多，常用的才能记住，要学会查找帮助文档

如果只记得命令或选项的开头部分，连续两个 `Tab` 可列出所有可能项

一些程序的安装须知等说明文件可能会放在：`usr/share/doc/...`

查看历史命令：`history | grep xxx`

常用命令：<https://wangchujiang.com/reference/docs/linux-command.html>

```shell
# 查看日历
cal 3 2019

# 查看时间
date -R
'
Fri, 03 Feb 2019 00:57:49 +0800
注释：+东区，-西区，所以此处为东八区
'
```

## tldr

Too Long; Didn’t Read.

简化 manual 手册，根据二八法则列出最常用的部分

在线查询：<https://tldr.inbrowser.app/>

## man

用户手册(manual)：<https://manned.org/>

格式：`man <key>`

```shell
# 查看 man 本身，即完整的用户手册
man man
'
用户手册按共划分为9章，当我们查看某个关键字的用户手册时，其实就是在这9章内容中去查找
    1   Executable programs or shell commands
        用户可从shell运行的命令
    2   System calls (functions provided by the kernel)
        必须由内核完成的功能
    3   Library calls (functions within program libraries)
        大多数libs函数
    4   Special files (usually found in /dev)
        特殊文件，通常在/dev中查找
    5   File formats and conventions, e.g. /etc/passwd
        人类可读的文件的格式说明，比如/etc/passwd
    6   Games
        游戏相关
    7   Miscellaneous (including macro packages and conventions), e.g. man(7), groff(7), man-pages(7)
        文件系统标准描述，网络协议，ASCII和其它字符集，还有你眼前这份文档以及其他东西
    8   System administration commands (usually only for root)
        系统命令，通常只能由root执行
    9   Kernel routines [Non standard]
        这是废弃的章节，原本想把一些关于内核的文件放在这里

如果关键字重名，比如passwd既可以指进行用户密码设置的命令，也可以指/etc/passwd配置文件
    那么直接查看可能就无法分清，此时可以加上章节
        man 1 passwd  即查看第1章passwd命令的手册
        man 5 passwd  即查看第5章/etc/passwd配置文件的手册

h 查看手册的帮助
回车 滚动下一行
空格 滚动下一屏
b 往前一屏
查找
    向下查找：/string
    向上查找：?string
q 退出手册
'
```

如果我们只知道key，不知道它属于什么类型时

- `man -f key` 精准匹配所有key相关的章节，简写：`whatis key`
- `man -k key` 模糊查询包含key的相关章节，简写：`apropos key`
- `man -a key` 列出所有的手册，挨个查看，如果不是则跳过(ctrl-d)，继续展示下一个

![20210821144749](https://image.zuoright.com/20210821144749.png)

## help

命令分为内建（shell自带的命令）或外部命令，可以用 `type <cmd>` 查看

当我们查看内建命令的用户手册时，有些系统会返回shell的手册，而有些会返回空

比如 cd 命令属于内建命令，可以用 help 查看帮助

- 查看内部命令：`help <cmd>`
- 外部命令：`<cmd> --help` （内部命令貌似也可以用）

## info

info 是对 help 的补充，比 help 更详细，貌似就是用户手册的内容
