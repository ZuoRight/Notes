# 上下文管理器

在 Python中，上下文管理器（Context Manager）是一种用于管理资源的对象，这些资源在进入和退出时分别做一些预处理和清理的工作。

with 语句用于管理和调用上下文管理器对象，它会设置一个临时的上下文，交给上下文管理对象控制，并且负责清理上下文。

可以嵌套使用

```python
with A() as a, B() as b:
    pass

# 等价于
with A() as a:
    with B() as b:
        pass
```

## 内置的上下文管理器

Python 提供了许多内置的上下文管理器，用于管理各种资源，如文件操作、线程同步、异常处理、标准输出重定向、临时文件、数据库连接、压缩文件等。

通过使用这些内置的上下文管理器，可以确保资源在使用过程中得到正确的管理和释放，写出更加健壮和优雅的代码。

### 文件操作

```python
with open("filename.xx") as fp:
    """
    1. open() 返回 TextIOWrapper 类的实例，with 将其交给上下文管理器
    2. 调用 TextIOWrapper 的 __enter__ 方法，返回 self
    3. as 语句将 fp 绑定到返回值 self，as 子句其实是可选的，但对于 open() 函数是必须的，以便获取文件的引用
    """
    src = fp.read(60)
    """
    4. 调用 TextIOWrapper 的 __exit__ 方法，扮演 finally 子句的角色
    """
```

如果不使用 with 打开文件，则需要使用 `try...finally` 来处理报错

```python
file = open('example.txt', 'r')
try:
    content = file.read()
    print(content)
finally:
    file.close()
```

处理压缩文件

```python
import tarfile

with tarfile.open('example.tar.gz', 'r:gz') as tar_file:
    tar_file.extractall('extracted')
```

### 数据库连接

```python
import sqlite3

with sqlite3.connect('example.db') as conn:
    cursor = conn.cursor()
    cursor.execute('CREATE TABLE IF NOT EXISTS example (id INTEGER PRIMARY KEY, value TEXT)')
    cursor.execute('INSERT INTO example (value) VALUES (?)', ('Hello, world!',))
    conn.commit()
```

### 线程锁定

```python
import threading

lock = threading.Lock()

with lock:
    # 执行线程安全的代码
    pass
```

## 自定义上下文管理器

自定义上下文管理器，即自定义上下文管理协议中的两个方法：`__enter__` 和 `__exit__`

```python
class MyContextManager:
    def __enter__(self):
        print("Entering the context")
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        """
        __exit__ 有三个参数：exc_type, exc_val, exc_tb
            如果调用代码块未发生异常，则三个参数全为 None
            否则分别对应异常的 type、value 和 traceback
        __exit__ 的返回值可以为 True 或 False
            如果为 True，那么表示异常被忽视，相当于进行了 try-except 操作
            如果为 False，则该异常会被抛出
        """
        print("Exiting the context")
        if exc_type is not None:
            print(f"An exception occurred: {exc_val}")
        return True  # Suppress the exception

with MyContextManager():
    print("Inside the context")
    # raise ValueError("An error occurred")  # Uncomment to test exception handling
```

```python
import sqlite3

class DatabaseConnection:
    def __init__(self, db_name):
        self.db_name = db_name
    
    # 连接数据库
    def __enter__(self):
        self.conn = sqlite3.connect(self.db_name)
        return self.conn

    # 关闭数据库
    def __exit__(self, exc_type, exc_val, exc_tb):
        self.conn.close()

with DatabaseConnection('example.db') as conn:
    cursor = conn.cursor()
    cursor.execute('SELECT * FROM example_table')
    rows = cursor.fetchall()
    print(rows)
```

## 装饰器 `@contextmanager` 实现

装饰器 `@contextmanager` 可以把生成器函数变成上下文管理器，此时的 `yield` 关键字与迭代没有任何关系

```python
from contextlib import contextmanager

@contextmanager
def my_context_manager():
    print("Entering the context")
    yield
    print("Exiting the context")

# 也需要使用 with 来调用
with my_context_manager():
    print("Inside the context")

"""
Entering the context
Inside the context
Exiting the context
"""
```
