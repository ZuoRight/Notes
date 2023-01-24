# 引言

任务，是操作系统中最基本的调度单位，一个任务（进程）至少包含一个主任务（主线程），也可以包多个子任务（子线程）

考虑到CPU和IO之间巨大的速度差异，一个任务在执行的过程中大部分时间都在等待IO操作，所以需要多任务模型来支持多任务的并发或者并行

## 并发和并行

- 并发(Concurrency)：同一时间段多任务交替执行，同一时刻只有一个任务在执行，比如：多线程（同步）、协程（异步）

![20220715163537](http://image.zuoright.com/20220715163537.png)

- 并行(Parallel)：同一时刻多任务同时执行，比如：多进程

![20220715163522](http://image.zuoright.com/20220715163522.png)

并发其实是比并行更广泛的一个概念

![20230124171152](http://image.zuoright.com/20230124171152.png)

真正的多任务并行，是指一个CPU对应一个任务，当任务数大于CPU核心数时，操作系统会将多个任务轮流分配给CPU，交替执行，由于CPU运行速度很快，感觉上就像多个任务同时在执行，也就是说单核CPU也可以执行多任务。

> CPython的GIL全局锁限制只能在单核上并发

如何调度进程和线程，完全由操作系统决定，程序自己不能决定什么时候执行，执行多长时间。

多进程和多线程的程序涉及到相互通信、协调同步、数据共享的问题，编写起来更复杂

## 同步和异步

CPU和内存的处理速度远远高于磁盘/外设，比如CPU输出100M的数据可能如果要0.01秒，可是磁盘接收这100M的数据可能需要10秒

同步和异步的区别就在于系统是否等待I/O执行的结果

- 同步(sync)：同步就是顺序执行，需要等待执行完当前任务再执行下一个
- 异步(async)：异步就是系统会先告诉应用程序请求已经收到，随后再去异步处理，等处理完成后，系统再通过事件通知的方式告诉应用程序结果

使用异步I/O来编写程序性能会远远高于同步I/O，但是异步I/O的缺点是编程模型复杂，必须得知道什么时候通知，而且通知的方法也有很多种

- 回调模式：服务员跑过来找到你
- 轮询模式：服务员发短信通知你，你就得不停地检查手机

从调用者（应用程序）的角度可分为

- 阻塞：是指应用程序在执行 I/O 操作后，如果没有获得响应，就会阻塞当前线程，不能执行其他任务
- 非阻塞：是指应用程序在执行 I/O 操作后，不会阻塞当前的线程，可以继续执行其他的任务，然后被回调

## 选择

```python
# 如果是CPU密集型
if cpu_heavy:
    print('使用 multi-processing 来提高程序运行效率')
# 如果是I/O密集型，则考虑使用并发
else:
    # 如果I/O操作很慢
    if io_slow:
        print('则需要协程Asyncio')
    # 如果I/O操作很快，任务量有限
    else:
        print('使用多线程即可 multi-threading')

# 理论上最高效的方式应该是：多进程 + 协程
```

- CPU密集型任务(CPU-bound tasks)

CPU密集型任务的特点是要进行大量的计算，消耗CPU资源，比如计算圆周率、对视频进行高清解码等等，全靠CPU的运算能力。要最高效地利用CPU，任务数量应当等于CPU的核心数。

CPU密集型任务由于主要消耗CPU资源，因此，代码运行效率至关重要。Python这样的脚本语言运行效率很低，完全不适合CPU密集型任务，最好用C语言编写。

- IO密集型任务(IO-bound tasks)

常见的大部分任务都是IO密集型任务，比如Web应用、磁盘读写等，这类任务的特点是CPU消耗很少，任务的大部分时间都在等待IO操作完成。（因为IO的速度远远低于CPU和内存的速度）

IO密集型任务执行期间，99%的时间都花在IO上，花在CPU上的时间很少，首选开发效率最高的脚本语言。