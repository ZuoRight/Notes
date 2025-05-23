# 进程间通信

IPC, Inter Process Communication 进程间通信，指至少两个进程或线程间传送数据或信号的一些技术或方法。

<https://time.geekbang.org/column/article/576947>

## 环境变量

environment variable，以键值对的形式存在，键名保持唯一

可以让两个进程共享某个简单的文本信息

```shell
# 使用 export 设置环境变量
# 由于键名唯一，所以设置同名环境变量时，即相当于修改
export key1=value1
export key2="value 2"  # 有空格需要使用双引号
```

在某个进程中设置的环境变量，其可见范围仅限于当前进程及其子进程

查看当前进程可见的所有环境变量：`env`

使用 export 设置的环境，在进程结束后则会失效，如果想永久保存，需要将 export 命令添加到初始化配置文件中

## 管道 Pipe

管道只能用于无格式字节流，并且缓冲区大小受限

### 匿名管道

chained pipeline，主要用于有亲缘关系的进程间通信，把父进程的 stdout 传给子进程的 stdin

```shell
ls | grep "xxx"
netstat -an | grep "LISTEN" | grep "8547"
```

用匿名管道串联起多个进程就构成一个进程组（jobs）

### 具名管道

Name Pipe，即有名字的管道，可以允许非亲缘关系进程间通信

具名管道也称为FIFO（First-In-First-Out），可以在文件系统中创建一个特殊文件，用于进程间通信

```shell
mkfifo demo  # 创建具名管道
echo "hello, test" > demo  # 向管道中写入数据
cat demo  # 从管道中取出数据
rm demo  # 删除管道
```

## 信号 Signal

可以通知目标进程（还可以通知自己）某种事件发生

```shell
kill -9 pid
```

## 信号量 Semaphore

用于两个进程之间同步协作手段，它相当于操作系统提供的一个特殊变量，程序可以在上面进行`wait()`和`notify()`操作

## 共享内存

允许多个进程访问同一块公共的内存空间，这是效率最高的进程间通信形式。

## 消息队列 Message Queue

### 系统级消息队列

如 POSIX 消息队列、System V 消息队列

进程可以向队列添加消息，被赋予读权限的进程则可以从队列消费消息。

### 分布式消息队列

RabbitMQ、Kafka 等，专门设计用于跨网络、跨机器的消息传递

## 套接字

### 本地套接字 IPC Socket

出于效率考虑，当仅限于本机进程间通信时，套接字被优化为不会经过网络协议栈，不需要打包拆包、计算校验和、维护序号和应答等操作，只是简单地将应用层数据从一个进程拷贝到另一个进程，这种进程间通信方式有个专名的名称：UNIX Domain Socket，又叫做 IPC Socket，也叫本地套接字。

### 网络套接字

Sockets 可用于不同机器之间的进程通信。
