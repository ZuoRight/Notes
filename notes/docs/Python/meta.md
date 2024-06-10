# 元编程

元编程是一种编程技术，允许程序在运行时检查和修改自身的结构和行为。

- 内省（Introspection）：指在运行时检查对象的类型和属性的能力，回答「是什么」的问题
- 反射（Reflection）：包含内省，不仅可以检查，还可以修改对象的结构和行为，回答「如何做」的问题。

## 内省

- 可迭代的（Iterable），表示一个对象可以用 `for` 循环语句迭代其中的每个元素，还可以通过 `iter()` 方法转变为迭代器。
- 可重复的，表示一个对象的元素可以相同。
- 可下标的，表示一个对象可以通过下标的方式索引，还可以使用切片功能。
- 可哈希的（Hashable），表示一个对象的哈希值在其生命周期内绝不改变。

### 类型判断

- `type()`

```python
type(None)  # <class 'NoneType'>
type(True)  # <class 'bool'>

type(123)  # <class 'int'>
type(1.2)  # <class 'float'>
type(1 + 2j)  # <class 'complex'>

type(b'_str')  # <class 'bytes'>
type(_str)  # <class 'str'>

type(_tuple)  # <class 'tuple'>
type(_list)  # <class 'list'>
type(_set)  # <class 'set'>
type(_dict)  # <class 'dict'>
```

```python
# 直接与list类比较
if type(_list) == list:
    pass

# 转换为字符串后比较
if str(type(_dict)) == "<class 'dict'>":
    pass
```

- `isinstance()`

```python
isinstance(object, object_type)
isinstance(object, (int,float))  # 同时判断是否属于多种类型中的一种
```

还可以进一步判断是否为可迭代的、可反转的、可哈希的、迭代器、生成器等。

通常需要引入内置库 [collections.abc](https://docs.python.org/zh-cn/3.9/library/collections.abc.html)

> abc 是 抽象基类（Abstract Base Classes）的意思

```python
from collections.abc import Iterable, Reversible, Hashable, Iterator, Generator

_num = 123
_str = "123"
_tuple = (1,2,3)
_list = [1, 2, [31, 32]]
_set = {1,2,3}
_dict = {"a":1,"b":2,"c":3}


# 数值、字符串、元组等不可变的都是可哈希的
print(isinstance(_num, Hashable))  # True
print(isinstance(_str, Hashable))  # True
print(isinstance(_tuple, Hashable))  # True

print(isinstance(_list, Hashable))  # False
print(isinstance(_set, Hashable))  # False
print(isinstance(_dict, Hashable))  # False


# 除了数值型都是可迭代的
print(isinstance(_str, Iterable))  # True
print(isinstance(_tuple, Iterable))  # True
print(isinstance(_list, Iterable))  # True
print(isinstance(_set, Iterable))  # True
print(isinstance(_dict, Iterable))  # True

print(isinstance(_num, Iterable))  # False


# 序列都是可反转的
print(isinstance(_str, Reversible))  # True
print(isinstance(_tuple, Reversible))  # True
print(isinstance(_list, Reversible))  # True

print(isinstance(_num, Reversible))  # False
print(isinstance(_set, Reversible))  # False
print(isinstance(_dict, Reversible))  # False

# 是否为迭代器
isinstance(x, Iterator)

# 是否为生成器
isinstance(x, Generator)
```

## 反射

可以用来动态地调用对象的方法、创建实例、以及修改类定义。

## 装饰器

装饰器可以用于动态地向类添加方法，从而实现 Mixin 的效果。

```python
# 创建一个混入类
class LoggerMixin:
    def log(self, message):
        print(f"日志: {message}")


import time
# 创建一个类装饰器
def measure_performance(cls):
    # 对类 cls 进行一些操作
    class WrappedClass(cls):
        def __init__(self, *args, **kwargs):
            super().__init__(*args, **kwargs)

        def __getattribute__(self, name):
            orig_attr = super().__getattribute__(name)
            if callable(orig_attr):
                def new_func(*args, **kwargs):
                    start_time = time.time()
                    result = orig_attr(*args, **kwargs)
                    end_time = time.time()
                    print(f"执行 {name} 耗时: {end_time - start_time} 秒")
                    return result
                return new_func
            return orig_attr
    # 返回 cls 或返回一个新的类
    return WrappedClass


# 使用类装饰器与混入类
@measure_performance
class MyTask(LoggerMixin):
    def process_task(self):
        self.log("任务开始执行")
        # 执行一些操作
        time.sleep(1)  # 模拟耗时操作
        self.log("任务执行结束")

# 使用类
task = MyTask()
task.process_task()
```

## 装饰器 decorator

装饰器是一种语法糖，可以使代码松耦合，能够在不修改原有业务逻辑的情况下对代码进行扩展。

一个函数前面可以加若干装饰器，类也有装饰器

```python
# 装饰器函数，参数，内容，返回都是函数
def decorator(func):
    # 闭包
    def wrapper(*args, **kwargs):
        print("前置扩展内容，调用装饰器，实现一些权限校验、用户认证等功能")

        demo(*args, **kwargs)  # 使用装饰器的函数被传入到这里，使用可变参数让其更加灵活

        print("后置扩展内容，调用装饰器，实现一些日志记录、性能测试、事务处理、缓存等")

    return wrapper

# 调用装饰器
"""最本质的方式"""
def demo(x):
    pass

decorator(demo_func)(x)

"""语法糖"""
@decorator
def demo(x):
    pass

demo(x)
```

参考：[Python核心技术与实战-强大的装饰器（极客时间）](https://time.geekbang.org/column/article/100914?utm_source=infoq&utm_medium=sitenavigation)
