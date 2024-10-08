# 引言

程序（Program）是静态的，是存储在磁盘或其他存储媒介上的一系列指令和数据，当程序被操作系统加载到内存中并开始执行时，被实例化为一个动态的具有生命周期的进程（Process）

进程是资源分配的最小单位，线程是进程内的执行单元，是 CPU 调度的最小单位。

无论进程还是线程，其实都是以任务的形态存在，任务通常用于描述一个正在执行的单一进程或线程。

![20240412113956](https://image.zuoright.com/20240412113956.png)

## 并发和并行

![20230124171152](http://image.zuoright.com/20230124171152.png)

真正的多任务并行（Parallelism），是指一个CPU对应一个任务，同一时刻多任务同时执行，比如：多进程

![20220715163522](http://image.zuoright.com/20220715163522.png)

但单核 CPU 也可以执行多任务，这是因为当任务数大于 CPU 核心数时，操作系统会将多个任务轮流分配给 CPU 交替执行，由于 CPU 运行速度很快，感觉上就像多个任务同时在执行，准确的讲，这属于并发（Concurrency），即同一时间段多任务交替执行，同一时刻只有一个任务在执行，比如：多线程（同步）、协程（异步）

![20220715163537](http://image.zuoright.com/20220715163537.png)

## 同步和异步

CPU 和内存的处理速度远远高于磁盘或外设等 I/O 设备 ，比如 CPU 输出 100M 的数据可能如果要 0.01s，可是磁盘接收这 100M 的数据可能需要 10s。

同步和异步的区别就在于系统是否等待任务执行的结果

同步(Synchronous)就是顺序执行，需要等待执行完当前任务再执行下一个

```text
Make the first request.
Wait.
Get the first response.

Make the second request.
Wait.
Get the second response.
```

异步(Asynchronous)就是系统会先告诉应用程序请求已经收到，随后再去异步处理，等处理完成后，系统再通过事件通知的方式告诉应用程序结果，通知的方式可分为

- 轮询模式：间隔一段时间主动请求或询问
- 回调模式：等待某个信号被告知后再调用

```text
Make the first request.
Make the second request.
Wait, event loop.
Get the first response.
Get the second response.
```

## 阻塞和非阻塞

老王爱喝茶，废话不多说，用水壶烧开水

同步 or 异步，是对于水壶而言。普通水壶≈同步，响水壶≈异步

阻塞(Blocking) or 非阻塞(Non-blocking)，是对于调用者老王而言。立等的老王≈阻塞，看电视的老王≈非阻塞。

- 同步阻塞：老王把水壶放到火上，立等水开。
- 同步非阻塞：老王把水壶放到火上，去客厅看电视，时不时去厨房看看水开没有。
- 异步阻塞：老王把响水壶放到火上，立等水开。
- 异步非阻塞：老王把响水壶放到火上，去客厅看电视，水壶响之前不再去看它了，响了再去拿壶。

一般异步是配合非阻塞使用的，否则没有意义。

## 多任务处理

- 多进程 Multi Processing

每个进程拥有独立的地址空间和资源，一个进程的崩溃不会直接影响到其他进程。进程之间的通信需要通过特定的进程间通信（IPC）机制来完成。

最理想的情况是任务数量等于CPU核心数

- 多线程 Multi Threading

每个线程有自己的执行序列、栈空间和线程局部存储，但同一进程中的线程间可以共享相同的地址空间和资源，所以线程间的上下文切换比进程间的上下文切换代价要小，但同时也意味着需要通过锁（如互斥锁）来控制对共享资源的访问，以避免竞态条件。

在某些编程语言中，特别是 CPython，存在全局解释器锁（GIL），这使得同一时间内只有一个线程可以执行 Python 字节码。也就是说在 Python 中多线程无法真正的并行，只能在单核 CPU 上并发。

- 协程 Asyncio

进程和线程的调度完全由操作系统决定，而协程是用户态的调度，调度方式是协作式的，而不是抢占式的，可以由程序自己决定什么时候执行，执行多长时间，所以避免了很多同步问题。

虽然协程也是交替并发执行，但协程的上下文切换只涉及保存和恢复少量的上下文信息，比线程上下文切换开销要小得多。

### 选择

理论上最高效的方式是：多进程 + 协程

```python
# 如果是 CPU 密集型
if cpu_heavy:
    print('使用多进程 multi-processing 来提高程序运行效率')
# 如果是 I/O 密集型
else:
    # 如果 I/O 操作很快，任务量有限
    if io_slow:
        print('使用多线程 multi-threading')
    # 如果 I/O 操作很慢
    else:
        print('则需要协程 Asyncio')
```

- CPU 密集型任务(CPU-bound tasks)

CPU 密集型任务的特点是，不进行网络通信或文件访问，而是进行大量的计算，主要消耗CPU资源，所以更适合多进程并行处理。

比如计算圆周率、对视频进行高清解码等等，全靠 CPU 的运算能力，因此，代码运行效率至关重要，通常使用运行效率更高的 C 语言等

![20230722173436](https://image.zuoright.com/20230722173436.png)

- I/O 密集型任务(IO-bound tasks)

I/O 的速度远远低于 CPU 和内存的速度，所以涉及网络通信或者文件访问的程序，比如 Web 应用、磁盘读写等，CPU消耗少，大部分时间都在等待I/O操作完成，使用多线程或者协程可以在等待 I/O 操作完成时挂起，切换到其他线程或协程继续执行。

另外通常使用开发效率更高的脚本语言来处理。

![20230722173449](https://image.zuoright.com/20230722173449.png)