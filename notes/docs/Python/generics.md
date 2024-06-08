# 泛型编程

Python 作为动态类型语言大多数时候是不需要泛型的。

通过泛型，可以编写更加通用和类型安全的代码。

## 类型注解

Python 从 3.5 版本开始，正式引入了类型注解，顾名思义就是标注变量或返回值的类型，不会改变 Python 的运行时行为，只是可以提高代码的可读性和易用性。

变量类型注解：用 `:type` 表示，函数返回值类型注解：用 `->type` 表示

```python
def func(x: int, y: int) -> int:
    """ 参数注释的正确格式
    :x：xxx
    :y：xxx
    """
    return x + y
```

对于复杂的数据结构需要借助 typing 模块来表达这些数据结构

```python
from typing import List, Tuple, Dict

names: List[str] = ['lily', 'tom']
version: Tuple[int, int, int] = (6, 6, 6)
operations: Dict[str, bool] = {'sad': False, 'happy': True}
```

## `TypeVar('T')`

```python
def func(value: int | float) -> int | float:
    return value
```

如上代码的类型注解，虽然可以表示函数能接收 int 或 float 类型的参数，然后返回 int 或 float 类型的数据，但是无法准确地知道输入 int 时，一定会输出 int 还是 float。

于是便需要引入泛型来解决

```python
# TypeVar 用于定义类型变量，它是泛型编程的核心工具
from typing import TypeVar

# 创建一个代表任意类型的变量 T
T = TypeVar('T')

# 定义泛型函数，接受一个T类型的参数value，并返回相同类型的值
def func(value: T) -> T:
    return value

func(1)  # 1
func(0.1)  # 0.1
```

关于 `TypeVar('T')` 中的 `T` 可以是任何名字，只是通常使用 T 或 U，也可以使用描述性名称，这样在类型检查工具（比如 mypy）中的错误信息会更友好一些。

```python
from typing import TypeVar, List

T = TypeVar('SubscriptableType')

def first_element(lst: List[T]) -> T:
    return lst[0]

first_element([1, 2, 3])  # 输出: 1
first_element({1, 2, 3})  # 报错，Value of type variable "SubscriptableType" is not subscriptable
```

## `@singledispatch`

我们知道，Python 中不同数据类型的拼接方式不太相同

如果想写一个通用方法来实现不同数据类型的数据拼接，需要做各种类型检查

```python
def check_type(func):
    def wrapper(*args):
        arg1, arg2 = args[:2]
        if type(arg1) != type(arg2):
            return '【错误】：参数类型不同，无法拼接!!'
        return func(*args)
    return wrapper

@check_type
def add(obj, new_obj):
    if isinstance(obj, str) :
        obj += new_obj
        return obj

    if isinstance(obj, list) :
        obj.extend(new_obj)
        return obj

    if isinstance(obj, dict) :
        obj.update(new_obj)
        return obj

    if isinstance(obj, tuple) :
        return (*obj, *new_obj)

print(add('hello',', world'))
print(add([1,2,3], [4,5,6]))
print(add({'name': 'wangbm'}, {'age':25}))
print(add(('apple', 'huawei'), ('vivo', 'oppo')))

# list 和 字符串 无法拼接
print(add([1,2,3], '4,5,6'))
```

如果使用泛型，则不需要检查类型

Single Dispatch 是一种基于参数类型的多态，允许你定义一个函数，然后根据传入参数的类型自动调用相应的具体实现

1. 定义一个泛型函数：使用 `@singledispatch` 装饰器定义一个通用函数。
2. 注册具体类型的函数实现：使用 `@<generic_function>.register(type)` 装饰器为不同类型注册具体的函数实现。
3. 调用泛型函数：根据参数的类型，自动调用相应的函数实现。

```python
from functools import singledispatch


def check_type(func):
    def wrapper(*args):
        arg1, arg2 = args[:2]
        if type(arg1) != type(arg2):
            return '【错误】：参数类型不同，无法拼接!!'
        return func(*args)
    return wrapper


@singledispatch
def add(obj, new_obj):
    raise TypeError

@add.register(str)
@check_type
def _(obj, new_obj):
    obj += new_obj
    return obj

@add.register(tuple)
@check_type
def _(obj, new_obj):
    return (*obj, *new_obj)

@add.register(list)
@check_type
def _(obj, new_obj):
    obj.extend(new_obj)
    return obj

@add.register(dict)
@check_type
def _(obj, new_obj):
    obj.update(new_obj)
    return obj


add('hello',', world')  # hello, world
add([1,2,3], [4,5,6])  # [1, 2, 3, 4, 5, 6]
add({'name': '7c'}, {'age': 18})  # {'name': '7c', 'age': 18}
add(('apple', 'huawei'), ('vivo', 'oppo'))  # ('apple', 'huawei', 'vivo', 'oppo')

# list 和 字符串 无法拼接
add([1,2,3], '4,5,6')  # '【错误】：参数类型不同，无法拼接!!'
```
