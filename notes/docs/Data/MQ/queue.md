# Queue

```python
from queue import Queue

# maxsize默认为0不受限，一旦>0，而消息数又达到限制，q.put()也将阻塞
q = Queue(maxsize=0)

# 默认阻塞程序，等待队列消息，可设置超时时间
q.get(block=True, timeout=None)

# 发送消息：默认会阻塞程序至队列中有空闲位置放入数据
q.put(item, block=True, timeout=None)

# 等待所有的消息都被消费完
q.join()

# 通知队列任务处理已经完成
# 通常配合join使用，当所有任务都处理完成时，join()阻塞将会解除
q.task_done()

# 查询当前队列的消息个数
q.qsize()

# 队列消息是否都被消费完，返回True/False
q.empty()

# 检测队列里消息是否已满，返回True/False
q.full()
```

## 分类

### `queue.Queue` 先进先出队列

> FIFO，First in First Out

```python
import queue

q = queue.Queue()
for i in range(5):
    q.put(i)

while not q.empty():
    print q.get()

# 先进入队列的消息，将优先被消费
"""
0
1
2
3
4
"""
```

### `queue.LifoQueue` 后进先出队列

```python
import queue

q = queue.LifoQueue()

for i in range(5):
    q.put(i)

while not q.empty():
    print q.get()

# 后进入消息队列的，将优先被消费
"""
4
3
2
1
0
"""
```

### `queue.PriorityQueue` 优先级队列

```python
from queue import PriorityQueue

# 重新定义一个类，继承自PriorityQueue
class MyPriorityQueue(PriorityQueue):
    def __init__(self):
        PriorityQueue.__init__(self)
        self.counter = 0

    def put(self, item, priority):
        PriorityQueue.put(self, (priority, self.counter, item))
        self.counter += 1

    def get(self, *args, **kwargs):
        _, _, item = PriorityQueue.get(self, *args, **kwargs)
        return item


queue = MyPriorityQueue()
queue.put('item1', 2)
queue.put('item2', 5)
queue.put('item3', 3)
queue.put('item4', 4)
queue.put('item5', 1)

while True:
    print(queue.get())

# 优先级高的先被消费
"""
item5
item1
item3
item4
item2
"""
```

## 示例

```python
from threading import Thread
from queue import Queue
import time


class CallManager(Thread):
    def __init__(self, students_all, students_come=[]):
        super().__init__()
        self.students_all = students_all
        self.students_come = students_come
        total = len(self.students_all)
        print(f'咱班一共有{total}人')
        self.queue = Queue(maxsize=total)
        [self.queue.put(s) for s in self.students_come]

    def run(self):
        if self.queue.full():
            print("人齐，不点名了")
        else:
            call_list = [self.queue.get() for i in range(0, self.queue.qsize())]
            print(f'看着不齐昂，点下名...')
            for student_name in self.students_all:
                print(f"老师：{student_name}来了没？")
                time.sleep(1)
                print(f"{student_name}: 到！") if student_name in call_list else print(f"{student_name}没来哟！")
            if self.queue.empty():
                print("点名结束，上课！")



students_all = ["小富", "小强", "小民", "小主"]
students_come = ["小富", "小强", "小主"]
thread_cm = CallManager(students_all, students_come)
thread_cm.start()
```
