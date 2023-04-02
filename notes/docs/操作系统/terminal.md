# 终端

终端(Terminal)只是基于文本的输入输出机制，需要引入Shell来解释具体的命令及其语法

当 Shell 启动了某个进程，会调用某个系统API（e.g. `tcsetpgrp`）把进程编号PID与Shell所属的终端关联起来，当终端需要发送信号时，会调用某个系统API（`tcgetpgrp`）获取进程的编号

## 发展演变

![20230331133529](http://image.zuoright.com/20230331133529.png)

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

## 三种缓冲模式

- 字符模式：每按一个键就会立即发送，无缓冲，基本淘汰
- 行模式：按下回车键后发送一整行，用的最多
- 屏模式：缓存当前屏幕内容，支持方向键移动，用的较少

## 标准流

数据流，可类比为水管，用于传输数据

POSIX 系统中，每个进程都内置了三个标准数据流

- 标准输入流：`stdin`
- 标准输出流：`stdout`
- 标准错误流：`stderr`

当进程启动后，stdin 默认对接到终端的输入，stdout & stderr 默认对接到终端的输出

![20230401234228](http://image.zuoright.com/20230401234228.png)

在程序中打开某个文件，会得到一个文件描述符 `fd`(file descriptor)，可以通过fd对文件进行读写，进程的标准流相当于三个特殊的fd，在操作系统启动时就已经准备好了，分别对应 0、1、2 三个描述符

## 重定向

### stdin重定向

输入重定向是指将一个命令的输入从键盘改为来自文件或另一个命令的输出

- `<` 将一个文件的内容作为命令的输入
- `<<` 允许用户从终端输入多行文本，直到输入一个特定的分界符为止
- `<<<` 将一个字符串作为命令的输入

```shell
command < input_file
wc -l < /etc/xxx  # 统计输入行数

# 直到输入 EOF 为止，并将这些文本作为 sort 命令的输入进行排序
sort << EOF
line 3
line 1
line 2
EOF

# 将字符串 "hello world" 作为 grep 命令的输入，并在其中查找单词 "world"
grep world <<< "hello world"
```

### stdout重定向

输出重定向是指将命令的输出从终端改为写入到文件或作为另一个命令的输入

- `>` 表示覆盖
- `>>` 表示追加

```shell
ls > filelist.txt  # 覆盖写入
date >> logfile.txt  # 追加写入

# 执行cat，命令行等待输入，Ctrl+d退出时输入内容自动保存到file
cat > file

# 从file1读取内容写入到file2
# 类似于复制（仅仅复制了内容，但不能复制属性，所以无法替代cp）
cat < file1 > file2
cat > file2 < file1

# 命令行输入多行，遇到EOF结束输入，内容保存到file
cat > file << EOF
```

### stderr重定向

- `2>`

```shell
cmd 2> file

# 将错误信息丢弃
cmd 2> /dev/nul  # /dev/null是一个特殊文件，所有传给它的东西它都丢弃掉
```

### stderr 重定向到 stdout

- `2>&1`

```shell
ls /path/to/nonexistent 2>&1
```

### stderr 和 stdout 一起重定向到文件

- `&>` 首选写法
- `>&` 老式写法

```shell
ls /path/to/nonexistent &> output.txt

# 另一种写法
cmd > file 2>&1  # 1重定向到file，2重定向到1，最终一起重定向
cmd > /dev/null 2>&1  # 等价于 cmd &>，将所有输出丢弃

# 错误写法
cmd 2>&1 > file  # 如果先把2重定向到1，只有1会被重定向到file
cmd >> file 2>> file  # 这样虽然可以都保存但会交叉的混在一起
```

## 快捷键

- `Tab` 命令及选项补全/提示
- `Ctrl`+`c` 中断目前程序
- `Ctrl`+`d` 键盘输入结束EOF(End Of Input)
- `Ctrl`+`s` 打开/关闭回显（即输入不显示在屏幕上，常见于输入密码时）
- `Shift`+`Page Up/Down` 向前/后翻看输出信息，`clear`后则翻不出来
