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
- shell 用于解释用户对操作系统的操作

### 赋予执行权限

- 创建文件时默认带了读写权限，查看权限：`ls -l`
- 加执行权限：`chmod u+x filename`

## 常用命令

执行多条命令，用分号隔开

```shell
echo "输出内容"  # 单引号，双引号区别

ls [-al] # 查看文件，加-a可查看隐藏文件，-l可查看文件权限等详情
```

文件描述符，通常用1~9的数字表示一个文件

- 0 标准输入stdin，输入自键盘
- 1 标准输出stdout，输出到屏幕
- 2 标准错误输出stderr

输入/输出重定向

```shell
# 输出重定向，是1>的简写
cmd > file  # 将命令的输出从屏幕重定向到file文件，file若非空，则默认覆盖
cmd >> file  # file若非空，则追加写入

# 将错误信息保存到file
cmd 2> file
cmd 2> /dev/nul  # 将错误信息丢弃

# 同时将错误信息保存到file
cmd &> file  # 最简写法
cmd > file 2>&1  # 另一种写法
cmd 2>&1 > file  # 错误写法，如果先把2指向1，即屏幕，再指向file，只是1指向了file，2其实没变，还是指向屏幕
cmd > file 2> file  # 错误写法，这样虽然可以都保存但会交叉的混在一起
```

用cat创建文件

```shell
cat  # 执行cat什么都不加则会让在命令行输入内容，ctrl+d退出
cat > file  # 回车后在命令行输入内容，退出则内容自动保存到file中

# 输入重定向，是0<的简写
# 原本由键盘输入的数据，改由文件内容来代替
cat > file1 < file2  # 从file2读取内容写入到file1中，类似于复制

# 设置输入结束符
cat > file << "eof"
```