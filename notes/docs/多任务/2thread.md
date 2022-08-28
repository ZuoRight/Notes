# 线程 Thread

> 示例代码：<https://github.com/ZuoRight/demo-python/tree/master/multitask>

多线程中，所有变量都由所有线程共享，所以，任何一个变量都可以被任何一个线程修改，因此，线程之间共享数据最大的危险在于多个线程同时改一个变量，把内容给改乱了。

可以加线程锁，锁的好处就是确保了某段关键代码只能由一个线程从头到尾完整地执行，坏处当然也很多，首先是阻止了多线程并发执行，包含锁的某段代码实际上只能以单线程模式执行，效率就大大地下降了。其次，由于可以存在多个锁，不同的线程持有不同的锁，并试图获取对方持有的锁时，可能会造成死锁，导致多个线程全部挂起，既不能执行，也无法结束，只能靠操作系统强制终止。

Python 的解释器并不是线程安全（thread-safe）的，为了解决由此带来的 race condition 等问题，Python 便引入了GIL锁（Global Interpreter Lock，全局解释器锁），任何Python线程执行前，必须先获得GIL锁，然后，每执行100条字节码，解释器就自动释放GIL锁，让别的线程有机会执行。这个GIL全局锁实际上把所有线程的执行代码都给上了锁，所以，多线程在Python中只能交替执行，即使100个线程跑在100核CPU上，也只能用到1个核。

> GIL是默认的CPython解释器设计历史遗留问题，要真正利用多核，可以用其他的解释器，比如PyPy、JPython等

这导致了多线程无法利用多核。多线程的并发在Python中就是一个美丽的梦。对于IO密集型多线程是有效的

```python
import threading

# 创建一个线程
t = threading.Thread(target=func)

# 启动子线程
t.start()

# 阻塞子线程，待子线程结束后，再往下执行
t.join()

# 判断线程是否在执行状态，在执行返回True，否则返回False
t.is_alive()
t.isAlive()

# 设置线程是否随主线程退出而退出，默认为False
t.daemon = True
t.daemon = False

# 设置线程名
t.name = "My-Thread"

# 获取线程id
threading.get_ident()
# 获取当前线程
threading.current_thread()
# 获取当前线程名字
threading.current_thread().getName()
```

Python 的 threading 模块消息通信机制主要有如下三种：

- Event
- Condition
- Queue

其中使用最多的Queue是线程安全的（实现了锁原语），不需要加锁
