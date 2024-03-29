# 栈与队列

## stack

![20240327233522](https://image.zuoright.com/20240327233522.png)

先进后出，比如浏览器中的后退与前进、软件中的撤销与反撤销。

### 内置栈

在 Python 中没有内置的栈类，可以将 list 当作栈使用，并忽略与栈无关的操作，可以视为一种受限制的数组或链表。

```python
# 初始化
stack: list[int] = []

# 入栈
stack.append(1)
stack.append(2)

# 出栈
stack.pop()

# 访问栈顶元素
stack[-1]

# 获取栈的长度
size: int = len(stack)

# 判断是否为空
is_empty: bool = len(stack) == 0
```

### 基于数组实现

```python
class ArrayStack:
    """基于数组实现的栈"""

    def __init__(self):
        """构造方法"""
        self._stack: list[int] = []

    def size(self) -> int:
        """获取栈的长度"""
        return len(self._stack)

    def is_empty(self) -> bool:
        """判断栈是否为空"""
        return self._stack == []

    def push(self, item: int):
        """入栈"""
        self._stack.append(item)

    def pop(self) -> int:
        """出栈"""
        if self.is_empty():
            raise IndexError("栈为空")
        return self._stack.pop()

    def peek(self) -> int:
        """访问栈顶元素"""
        if self.is_empty():
            raise IndexError("栈为空")
        return self._stack[-1]

    def to_list(self) -> list[int]:
        """返回列表用于打印"""
        return self._stack
```

### 基于链表实现

```python
class LinkedListStack:
    """基于链表实现的栈"""

    def __init__(self):
        """构造方法"""
        self._peek: ListNode | None = None
        self._size: int = 0

    def size(self) -> int:
        """获取栈的长度"""
        return self._size

    def is_empty(self) -> bool:
        """判断栈是否为空"""
        return not self._peek

    def push(self, val: int):
        """入栈"""
        node = ListNode(val)
        node.next = self._peek
        self._peek = node
        self._size += 1

    def pop(self) -> int:
        """出栈"""
        num = self.peek()
        self._peek = self._peek.next
        self._size -= 1
        return num

    def peek(self) -> int:
        """访问栈顶元素"""
        if self.is_empty():
            raise IndexError("栈为空")
        return self._peek.val

    def to_list(self) -> list[int]:
        """转化为列表用于打印"""
        arr = []
        node = self._peek
        while node:
            arr.append(node.val)
            node = node.next
        arr.reverse()
        return arr
```

## queque

![20240327234512](https://image.zuoright.com/20240327234512.png)

先进先出，比如打印机的任务队列、餐厅的出餐队列、购物订单队列

在 Python 中，虽然 `queue.Queue()` 是纯正的队列类，但不太好用，通常使用双向队列 `deque` 来当作队列使用

```python
from collections import deque

# 初始化队列
que = deque()

# 入队
que.append(1)
que.append(3)
que.append(2)
que.append(5)
que.append(4)

# 出队
que.popleft()

# 访问队首元素
que[0]

# 获取队列长度
len(que)
```

## double-ended queue

双向队列兼具栈与队列的逻辑，因此它可以实现这两者的所有应用场景，同时提供更高的自由度。

```python
from collections import deque

# 初始化双向队列
deque = deque()

# 入队
deque.append(2)  # 添加至队尾
deque.appendleft(3)  # 添加至队首

# 访问元素
deque[0]  # 队首元素
deque[-1]  # 队尾元素

# 元素出队
deque.popleft()  # 队首元素出队
deque.pop()  # 队尾元素出队

# 获取双向队列的长度
len(deque)
```
