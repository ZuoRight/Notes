# 协程 Coroutine

同步运行方式

```plain text
Make the first request.
Wait.
Get the first response.

Make the second request.
Wait.
Get the second response.
```

异步运行方式

```plain text
Make the first request.
Make the second request.
Wait, event loop.
Get the first response.
Get the second response.
```

协程被创造出来是用来解决异步任务的，核心概念就是函数或者一段程序能够被挂起，稍后在挂起的位置恢复，挂起和恢复是可控制的。

线程属于系统级别调度，而协程是程序员级别的调度。在协程中控制共享资源不需要锁机制，只需要判断状态就好了，减少了线程上下文切换的开销，由此可以提高性能。

传统的生产者-消费者模型是一个线程写消息，一个线程取消息，通过锁机制控制队列和等待，但一不小心就可能死锁。如果改用协程，Produce和Consumer协作完成任务，所以称为“协程”，在Java中称作“虚拟线程”。

用协程代替多线程和多进程是一个很好的选择，因为它吸引人的特性：主动调用/退出，状态保存，避免cpu上下文切换等。

Python v3.4之前需要通过第三方库`gevent`以同步逻辑来书写异步程序

Python v3.4引入异步IO标准库`asyncio`：<https://docs.python.org/3/library/asyncio.html>

## 基于生成器的协程

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

## async/await

Py v3.5 版本引入了`async`和`await`关键字来分别替代`@asyncio.coroutine`和`yield from`

- `async` 定义协程的关键字
- `await` 用于挂起阻塞的异步调用接口，后接coroutine/future对象
- `coroutine` 协程对象，使用关键字`async`声明的函数，需要注册到事件循环中被调用，被调用时会返回一个协程对象
- `event_loop` 事件循环，当满足事件发生时会调用被注册到事件循环的协程函数
- `Task` 任务是对协程的进一步封装，封装为一个Future对象
- `Future` 是Task的父类，代表将来执行或没执行的任务的结果

状态

1. Pending：创建future，还未执行
2. Running：事件循环正在调用执行任务
3. Done：任务执行完毕
4. Cancelled：Task被取消后的状态

```python
import asyncio

# 定义协程函数
async def demo(name):
    await asyncio.sleep(1)  # 工具函数，模拟IO阻塞，返回协程对象
    print(f"Hello, {name}")

cor = demo("7c")  # <coroutine object>，调用并不会运行函数

"""
loop = asyncio.get_event_loop()  # 定义事件循环对象
task = loop.create_task(cor)  # 将协程注册到事件循环任务
loop.run_until_complete(task)  # 触发事件循环执行任务
"""
asyncio.run(cor)  # 运行协程函数，等同于上面三行代码

# 检查是否是协程 Coroutine 类型
from collections.abc import Coroutine, Generator
print(isinstance(cor, Coroutine))  # True
print(isinstance(cor, Generator))  # False
```

多任务

```python
import asyncio

# 协程函数
async def do_some_work(x):
    print('Waiting: ', x)
    await asyncio.sleep(x)
    return 'Done after {}s'.format(x)

# 协程对象
coroutine1 = do_some_work(1)
coroutine2 = do_some_work(2)
coroutine3 = do_some_work(4)

# 将协程转成任务列表
tasks = [
    asyncio.ensure_future(coroutine1),
    asyncio.ensure_future(coroutine2),
    asyncio.ensure_future(coroutine3)
]

loop = asyncio.get_event_loop()

"""将多个协程注册到一个事件循环中"""
loop.run_until_complete(asyncio.wait(tasks))  # 方式1
loop.run_until_complete(asyncio.gather(*tasks))  # 方式2


"""
dones, pendings = await asyncio.wait(tasks)
for task in tasks:
    print('Task ret: ', task.result())

results = await asyncio.gather(*tasks)
for result in results:
    print('Task ret: ', result)
"""
```
