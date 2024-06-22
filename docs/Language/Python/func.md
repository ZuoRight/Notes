# 函数

```python
# 关键字def用于声明函数
# 函数在被调用前都是不存在的
def func_name(param1, param2):  # 定义时的参数叫做形参
    pass

    # 返回值
    # 可参略，省略则默认返回None
    # 可同时返回多个，以元组形式返回
    return param1, param2

# 调用函数
func(1, 2)  # 调用时的参数叫做实参
```

Python 中函数是一等对象，即

- 函数可以赋值给变量
- 函数能作为参数传递给函数
- 函数可以作为函数的返回对象

```python
r = f1
r(1, 2)
```

## 位置参数

```python
def add(a, b):
    return a + b

result = add(2, 3)  # 正确：a=2, b=3
```

### 位置参数解包

```python
def multiply(a, b, c):
    return a * b * c

args = (2, 3, 4)
print(multiply(*args))  # 输出: 24
```

### 给参数设置默认值

```python
# 默认值参数要放到后面
def greet(name, greeting="Hello"):
    return f"{greeting}, {name}!"

print(greet("Alice"))  # 输出: Hello, Alice!
print(greet("Bob", "Hi"))  # 输出: Hi, Bob!
```

### 数量不定的位置参数 `*args`

接收任意数量的非关键字参数，组成一个元组传递给函数

```python
def sum_all(*args):
    return sum(args)

print(sum_all(1, 2, 3, 4))  # 输出: 10
print(sum_all(5, 10))  # 输出: 15
```

## 关键字参数

```python
def describe(name, age, city):
    return f"{name} is {age} years old and lives in {city}."

# 实参顺序可以不与形参一致
print(describe(name="Alice", age=30, city="Wonderland"))
# 输出: Alice is 30 years old and lives in Wonderland
```

### 参数解包

```python
def introduce(name, age, city):
    return f"{name} is {age} years old and lives in {city}."

details = {"name": "Alice", "age": 30, "city": "Wonderland"}
print(introduce(**details))  # 输出: Alice is 30 years old and lives in Wonderland
```

### 数量不定的关键字参数 `**kwargs`

接收任意数量的关键字参数，组成一个字典传递给函数

```python
def print_details(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

print_details(name="Alice", age=30, city="Wonderland")
# 输出:
# name: Alice
# age: 30
# city: Wonderland
```

## 参数混合使用

### 仅限关键字参数 `*,`

可以避免参数传递顺序错误

```python
# * 后面的都是关键字参数，可以有默认值
def func(x, *, y, z=3):
    return x + y + z

print(func(1, y=2, z=5))  # 输出: 8
print(func(1, y=2))  # 输出: 6
print(func(1, 2, 3))  # 错误：TypeError: func() takes 2 positional arguments but 3 were given
```

`*args` 后的也都是关键字参数，必须以关键字参数形式传入

```python
def func(x, *args, z=0):
    pass

func(1, 2, 3, 4, z=8)
```

### 参数顺序

```python
# 同时接收任意数量的位置参数和关键字参数
def func(*args, **kwargs):
    pass
```

`*args` 必须在位置参数后，`**kwargs` 必须在所有参数最后

```python
def func(positional, *args, keyword, **kwargs):
    pass
```

## 作用域

函数内定义的变量默认都是局部变量

```python
a = 1  # 全局变量

def f1():
    print(a)  # 函数内部可以访问全局变量

    # 局部变量
    a = 2  # 可以和全局变量名字相同（不建议），但不会重新赋值
    b = 3

print(a)  # 全局变量 a 依然是 1
print(b)  # 函数外部无法访问函数内部的变量和函数
```

函数内无法对全局变量重新赋值

```python
a = 1
b = {}

def f1():
    print(a)  # UnboundLocalError: local variable 'a' referenced before assignment
    """
    上面这里之所以报错，是因为下面定义了一个局部变量 a，在编译时，系统会先将 a 判断为一个局部变量，然而又在赋值语句之前被打印，所以会报错
    """
    a = 1


def f2(a):  # 注意，这里有形参a，意味着先定义了句不比那两
    a = a + 1  # 定义局部变量a，形参a+1，赋值给局部变量a


def f3():
    # dict是可变的，增加元素不算重新赋值
    b["k1"] = "v1"
```

函数内如果想改变全局变量，需要使用 `global` 关键字

```python
a = 1

def f1():
    global a  # 这里声明了 a 是全局变量 a
    a = a + 1  # 这样就没问题了
```

嵌套的内层函数如果想改变外层函数的变量，需要使用 `nonlocal` 关键字

```python
a = 1

def f1():
    a = 2  # 局部变量

    def f2():
        nonlocal a  # 这里声明了 a 是外层函数 a，即局部变量 a
        a = a + 1

    r = f2()
    return r  # a=3
```

## 闭包

函数的返回值可以是一个函数，这个函数可以是外部函数，也可以是内部函数。如果返回了内部函数，且内部函数使用了外部函数的变量，那么称之为闭包。

也就是说必须要满足3个条件

1. 函数内部定义了一个内嵌函数
2. 内嵌函数引用了其外部函数中的变量
3. 外部函数返回了这个内嵌函数

闭包可以封装数据，使得数据只能通过特定函数访问，从而提高代码的封装性和安全性。

如果要定义的类只有一个方法，可以考虑使用闭包替代。

```python
def func_wai(msg):
    def func_nei():
        return msg  # 引用外部函数的变量
    return func_nei  # 返回内部函数，不要加()

r = func_wai("hello wold")
r()
```

## 递归

函数内可以嵌套函数，被嵌套的函数不能被外界直接调用，以此可以保证数据的隐私性

```python
# 外层函数
def print_msg():
    msg = "zen of python"

    # 嵌套函数
    def printer():
        print(msg)

    printer()  # 调用内部函数

another = print_msg()  # 直接输出msg
```

如果嵌套的是自己，则称之为递归函数

不同性能的计算机允许的最大递归次数不同，过深的递归会导致栈溢出。

## 回调函数

回调函数是指作为参数被其他函数调用的函数

回调函数的执行通常是异步的，也就是说，它不会立即执行，而是在某个特定的时机被调用。

```python
def do_task(callback):
    print("Performing a task...")
    callback()

def on_task_complete():
    print("Task completed!")

do_task(on_task_complete)
```

通常用于

- 异步操作：例如，进行文件读取、网络请求时，通过回调函数处理操作完成后的逻辑。
- 事件驱动编程：如 GUI 编程中的按钮点击、鼠标移动等事件。
- 函数式编程：将函数作为参数传递，用于高阶函数、装饰器等。

### 异步操作

在复杂的异步操作中，多个回调函数嵌套调用会导致代码难以维护，称为回调地狱。

可以通过使用 async/await 或 Promise 等方式来解决

```python
import time

def fetch_data(callback):
    print("Fetching data...")
    time.sleep(2)  # 模拟网络延迟
    callback("Data fetched")

def handle_data(result):
    print(result)

fetch_data(handle_data)
```

### 事件驱动

```python
def button_click_handler(event):
    print(f"Button clicked! Event: {event}")

def simulate_button_click(handler):
    event_info = "Button1"
    handler(event_info)

simulate_button_click(button_click_handler)
```

## 匿名函数

也叫 lambda 表达式：`f = lambda 输入: 输出`

lambda 语句只是语法糖，与 def 一样会创建函数对象

除了作为参数传给高阶函数之外，Python 中很少使用匿名函数

```python
def f(x):
    return x+1

# 对于这种简单的函数可以简化成 lambada 表达式
f = lambda x: x+1

# 调用方式一样
f(2)  # 3

# 也可以直接写成
(lambda x: x+1)(2)  # 3
```

```python
# 没有参数时可以这样写
f = lambda : 2  # 通常用来返回另一个函数

# 等价于
def f():
    return 2
```

## 高阶函数

接受函数为参数，或者把函数作为返回值的函数，被称作高阶函数

比如 `map()`、`filter()`、`reduce()`

这几个函数如今已经没有那么重要了，可以用更易读的方式替换

比如用列表推导式替代 map 和 filter

```python
# 替代 list(map(fact, range(6)))
[fact(n) for n in range(6)]
```

用 `sum()` 替代 reduce 求和性能更高更易读

### reduce

```python
from functools import reduce

reduce(function, iterable[, initializer]):
    """
    function：一个二元函数，它接受两个参数，并返回一个值。reduce 会依次将 iterable 中的元素应用到该函数上。
    iterable：一个可迭代对象，如列表、元组等。
    initializer（可选）：初始值。如果提供了 initializer，reduce 会将其作为第一个参数，然后开始聚合计算。
    """
    pass
```

- 聚合计算

求和、求积等

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]
sum_result = reduce(lambda x, y: x + y, numbers, 10)  # 10 + 15 = 25
```

- 字符串拼接

```python
from functools import reduce

words = ["Hello", "World", "Python", "is", "awesome"]
sentence = reduce(lambda x, y: x + " " + y, words)  # Hello World Python is awesome
```

- 列表合并

```python
from functools import reduce

list_of_lists = [[1, 2], [3, 4], [5, 6]]
flattened = reduce(lambda x, y: x + y, list_of_lists)  # [1, 2, 3, 4, 5, 6]
```

## 归约函数

把某个操作连续应用到序列的元素上，累计之前的结果，把一系列值归约成一个值

除了 `sum()` 和 `reduce()`，还有

-  `all(iterable)`

如果 iterable 的每个元素都是真值，返回 `True`

注意：`all([])` 返回 `True`

- `any(iterable)`

如果 iterable 中只要有元素是真值，就返回 `True`

注意：`any([])` 返回 `False`
