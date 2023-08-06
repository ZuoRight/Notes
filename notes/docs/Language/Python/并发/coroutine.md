# 协程 Coroutine

协程被创造出来是用来解决异步任务的，核心概念就是函数或者一段程序能够被挂起，稍后在挂起的位置恢复，挂起和恢复是可控制的。

线程属于系统级别调度，而协程是程序员级别的调度。在协程中控制共享资源不需要锁机制，只需要判断状态就好了，减少了线程上下文切换的开销，由此可以提高性能。

传统的生产者-消费者模型是一个线程写消息，一个线程取消息，通过锁机制控制队列和等待，但一不小心就可能死锁。如果改用协程，Produce和Consumer协作完成任务，所以称为“协程”，在Java中称作“虚拟线程”。

用协程代替多线程和多进程是一个很好的选择，因为它吸引人的特性：主动调用/退出，状态保存，避免cpu上下文切换等。

Python v3.4 之前需要通过第三方库 `gevent` 以同步逻辑来书写异步程序

Python v3.4 引入异步IO标准库 `asyncio`：<https://docs.python.org/3/library/asyncio.html>

asyncio 是单进程单线程的，不存在系统级上下文切换，即同时只能执行一个任务，它只是利用了等待时间来实现并发，核心是 event_loop，它决定了在众多可执行任务中选择执行哪一个，当一个任务结束时需要主动告诉 event_loop 可以让其它任务开始了，所以不存在竞争冒险的问题

asyncio 适用于需要等待的任务，比如网络通信（通常搭配 `aiohttp` 一起使用），如果没有等待的话，则协程并没有什么作用

```python
# 检查是否是协程类型
from collections.abc import Coroutine, Generator

isinstance(cor, Coroutine)  # True
isinstance(cor, Generator)  # False
```

## 基于生成器的协程（旧）

基于生成器的协程是 async/await 语法的前身，使用 `yield from` 语句创建的生成器可以等待Future和其他协程

> Deprecated since version 3.8, will be removed in version 3.11

```python
import asyncio

@asyncio.coroutine  # 协程标记，非强制
def hello():
    print("Hello world!")
    r = yield from asyncio.sleep(1)
    print("Hello again!")

cor = hello()  # <generator object>

# 获取EventLoop:
loop = asyncio.get_event_loop()
# 执行coroutine
loop.run_until_complete(cor)
loop.close()
```

## async/await（新）

Python v3.5 版本引入了 `async` 和 `await` 关键字，来分别替代 `@asyncio.coroutine` 和 `yield from`

- `coroutine` 协程函数，使用关键字 `async` 声明的函数，需要注册到事件循环中被调用，被调用时会返回一个协程对象
- `event_loop` 事件循环，当满足事件发生时会调用被注册到事件循环的协程函数
- `await` 用于挂起阻塞的异步调用接口，后可接 coroutine、task、future
- `Task` 任务是对协程的进一步封装，封装为一个 Future 对象
- `Future` 是Task的父类，代表将来执行或没执行的任务的结果

状态

1. Pending：创建future，还未执行
2. Running：事件循环正在调用执行任务
3. Done：任务执行完毕
4. Cancelled：Task被取消后的状态

```python
import asyncio
import time


# coroutine function
async def demo(delay, text):
    # 将task的控制权交给event_loop
    await asyncio.sleep(delay)  # 模拟IO阻塞，返回协程对象
    print(text)


# await coroutine object, 其实是同步的，需要等6s
async def main1():
    print(time.strftime("%X"))

    await demo(1, "aaaa")
    await demo(2, "bbbb")
    await demo(3, "cccc")

    print(time.strftime("%X"))


# await task, 是异步的，只需要等3s
async def main2():
    # 把 coroutine object 变成 task
    """
    创建异步任务有三种方式
    recommended: asyncio.create_task()
    low-level: asyncio.ensure_future()
    low-level: loop.create_task()
    """
    task1 = asyncio.create_task(demo(1, "aaaa"))
    task2 = asyncio.create_task(demo(2, "bbbb"))
    task3 = asyncio.create_task(demo(3, "cccc"))

    print(time.strftime("%X"))

    await task1  # 可以得到返回值
    await task2
    await task3

    # 将多个任务注册到 event_loop 可简化为
    # await asyncio.gather(task1, task2, task3)

    print(time.strftime("%X"))


# 简化
async def main3():
    tasks = [demo(1, "aaaa"), demo(2, "bbbb"), demo(3, "cccc")]

    print(time.strftime("%X"))

    # gather可以直接将 coroutine object 转为 task，可省略 create_task()
    result_list = await asyncio.gather(*tasks)  # 以list形式按序返回各task的值

    """
    也可以用 await asyncio.wait(tasks) 方式，但其将在 Python 3.11 版本移除
    """

    print(time.strftime("%X"))


# 与普通函数不同，调用协程函数并不会运行它，而是返回协程对象
coro = main3()

# 从正常的 synchronize 模式切换到 asynchronize 模式，即进入 event_loop，开始控制整个程序的状态
asyncio.run(coro)
"""
等同于以下两行代码
loop = asyncio.get_event_loop()  # 定义 event_loop
loop.run_until_complete(coro)  # 触发 event_loop 执行任务
"""
```

![20230804120924](https://image.zuoright.com/20230804120924.png)
