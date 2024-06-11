# 元编程

元编程是一种编程技术，允许程序在运行时检查和修改自身的结构和行为。

- 内省（Introspection）：指在运行时检查对象的类型和属性的能力，回答「是什么」的问题
- 反射（Reflection）：包含内省，不仅可以检查，还可以修改对象的结构和行为，回答「如何做」的问题。

可以理解为内省是了解和获取程序结构的工具，而反射则是基于这些了解进行实际操作和修改的能力。

## 内省

Python 提供了许多内置函数和模块，使得内省成为一种非常直观和容易使用的功能。

列表形式返回对象所有的属性和方法

- `dir(object)`

类型检查

- `type(object)` 返回对象的类型
- `isinstance(object, class)` 检查对象是否是指定类的实例

获取、设置或检查对象的属性

- `getattr(object, 'attribute')` 获取对象的属性值。
- `setattr(object, 'attribute', value)` 设置对象的属性值。
- `hasattr(object, 'attribute')` 检查对象是否有某个属性。

返回全局和局部命名空间中的字典

- `globals()` 返回一个包含当前全局符号表的字典。
- `locals()` 返回一个包含当前局部符号表的字典。

### `dir()`

```python
>>>  dir(__builtins__)

# 内置错误类型
['ArithmeticError', 'AssertionError', 'AttributeError', 'BaseException', 'BlockingIOError', 'BrokenPipeError', 'BufferError', 'BytesWarning', 'ChildProcessError', 'ConnectionAbortedError', 'ConnectionError', 'ConnectionRefusedError', 'ConnectionResetError', 'DeprecationWarning', 'EOFError', 'Ellipsis', 'EnvironmentError', 'Exception', 'False', 'FileExistsError', 'FileNotFoundError', 'FloatingPointError', 'FutureWarning', 'GeneratorExit', 'IOError', 'ImportError', 'ImportWarning', 'IndentationError', 'IndexError', 'InterruptedError', 'IsADirectoryError', 'KeyError', 'KeyboardInterrupt', 'LookupError', 'MemoryError', 'ModuleNotFoundError', 'NameError', 'None', 'NotADirectoryError', 'NotImplemented', 'NotImplementedError', 'OSError', 'OverflowError', 'PendingDeprecationWarning', 'PermissionError', 'ProcessLookupError', 'RecursionError', 'ReferenceError', 'ResourceWarning', 'RuntimeError', 'RuntimeWarning', 'StopAsyncIteration', 'StopIteration', 'SyntaxError', 'SyntaxWarning', 'SystemError', 'SystemExit', 'TabError', 'TimeoutError', 'True', 'TypeError', 'UnboundLocalError', 'UnicodeDecodeError', 'UnicodeEncodeError', 'UnicodeError', 'UnicodeTranslateError', 'UnicodeWarning', 'UserWarning', 'ValueError', 'Warning', 'ZeroDivisionError', 

# 这应该叫啥
'__build_class__', '__debug__', '__doc__', '__import__', '__loader__', '__name__', '__package__', '__spec__', 

# 内置函数
'abs', 'all', 'any', 'ascii', 'bin', 'bool', 'breakpoint', 'bytearray', 'bytes', 'callable', 'chr', 'classmethod', 'compile', 'complex', 'copyright', 'credits', 'delattr', 'dict', 'dir', 'divmod', 'enumerate', 'eval', 'exec', 'exit', 'filter', 'float', 'format', 'frozenset', 'getattr', 'globals', 'hasattr', 'hash', 'help', 'hex', 'id', 'input', 'int', 'isinstance', 'issubclass', 'iter', 'len', 'license', 'list', 'locals', 'map', 'max', 'memoryview', 'min', 'next', 'object', 'oct', 'open', 'ord', 'pow', 'print', 'property', 'quit', 'range', 'repr', 'reversed', 'round', 'set', 'setattr', 'slice', 'sorted', 'staticmethod', 'str', 'sum', 'super', 'tuple', 'type', 'vars', 'zip']
```

### `type()`

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

### `isinstance()`

```python
isinstance(object, object_type)
isinstance(object, (int,float))  # 同时判断是否属于多种类型中的一种
```

还可以进一步判断是否为可迭代的、可反转的、可哈希的、迭代器、生成器等。

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

动态地导入模块

`importlib.import_module(module_name)`

动态调用方法或访问对象的属性

```python
method = getattr(obj, 'method_name')
result = method()
```

删除对象的属性和方法

`delattr()`

动态执行 Python 代码

- `eval()`
- `exec()`

### `eval()`、`exec()`、`compile()`

`eval(object, [globals])`

用于字符串形式的执行单个表达式，不能做赋值循环等操作，返回表达式的结果

```python
y = eval("1+2")  # y=3
```

`exec(object, [globals])`

用于执行字符串形式的语句，返回 None

```python
y = exec("x=1+2")  # y=None, x=3
```

`compile(string, "", mode)`

可以将字符串编译为 `eval()` 和 `exec()` 可执行的格式，<https://segmentfault.com/q/1010000017171114>

个人理解通常代码很复杂或为了复用时才需要 `compile()` 预先编译，不然并没有什么卵用，直接使用 eval/exec 即可

```python
cmd = compile("1+2", "", mode="eval")
eval(cmd)  # 3
```

### `ast.literal_eval(_str)`

上面几个函数一定要慎用，可能会因为代码注入带来安全隐患

比如：`eval("__import__('os').system('rm -rf *')")`

如果字符串中是标准的 Python 数据类型，可以用 `ast.literal_eval()` 方法替代，它会判断内容是否为合法的 Python 类型，不是则会报错。

```python
import ast

_str = "_dcit"
# 如果_str="faker.name()"这种非合法python类型，则会报错
# 另外_str=""也会报错
_dcit = ast.literal_eval(_str)
```

## 元类

元类控制着类的创建、行为和特性，是用来创建类的类。

Python 中，所有类都是 `type` 的实例，包括 `object` 和 `type` 自己

> object 定义了一些基础方法和属性，比如 `__init__`、`__str__`、`__repr__` 等，是所有类的基类（除了它本身），包括 `type`（形成了闭环）

![20240611012144](https://image.zuoright.com/20240611012144.png)

```shell
>>> type(type)
<class 'type'>

>>> type(object)
<class 'type'>

>>> type(list)
<class 'type'>
```

也就是说所有类都是由 `type()` 创建的，当系统扫描到 `class` 关键字的时候，就会调用 `type()` 进行类的创建

```python
# 准备一个基类
class BaseClass:
    def talk(self):
        print("i am people")

# 准备一个方法
def say(self):
    print("hello")

# 使用type来创建User类
User = type("User", (BaseClass, ), {"name": "user", "say": say})
    """三个参数解析
    类的名称，若不指定，也要传入空字符串「""」
    元/父类，以 tuple 的形式传入，若没有父类也要传入空元组「()」，默认继承 (object, )
    绑定的方法或属性，以 dict 的形式传入
    """
```

通常大多数人都不会直接使用到元类，一般只有在框架设计、动态类生成等高级场景中使用，比如 Django 中的 ORM

## 抽象基类

<https://docs.python.org/3/library/collections.abc.html>

`collections.abc` 模块定义了一些常用的抽象基类（Abstract Base Classes, ABC），这些基类用于提供容器对象的基本接口，作为其他类实现接口时的超类或者说父类。

所谓的接口，就是类实现或继承的公开属性和方法，让对象在系统中扮演特定的角色

Python 中没有类似 `interface` 这样的关键字，而且除了抽象基类，每个类都有接口

![20240610225814](https://image.zuoright.com/20240610225814.png)

## 协议

协议可以理解为是非正式的接口，比如一个类只需实现 `__len__` 和 `__next__()` 两个方法，就属于迭代器，即使它只是行为看起来像迭代器。

> 这就是所谓的鸭子类型，不需要检查它是不是真的鸭子，只需要检查它的叫声和走路姿势像不像鸭子

鸭子类型的非正式协议虽然不严谨，但却简单高效，我们没必要为了让编译器高兴，只需要实现功能即可，保持 KISS 原则。

- 猴子补丁

协议是动态的，也就是说，即便对象一开始没有某个方法也没关系，可以后来再提供，比如运行时，甚至可以在交互式控制台中临时修改或扩展代码，而不改动源码，这种技术就叫猴子补丁（Monkey Patch），就像猴子一样灵活和随性。

## 装饰器 decorator

参考：[Python核心技术与实战-强大的装饰器（极客时间）](https://time.geekbang.org/column/article/100914?utm_source=infoq&utm_medium=sitenavigation)

装饰器是可调用的对象，其参数是另一个函数（被装饰的函数），装饰器可能会处理被装饰的函数，然后把它返回，或者将其替换成另一个函数或可调用对象。

装饰器可以使代码松耦合，能够在不修改原有业务逻辑的情况下对代码进行扩展。

一个函数可以同时添加多个装饰器。

```python
# 装饰器函数的参数、内容、返回都是函数
def decorator(func):
    # 闭包
    def wrapper(*args, **kwargs):
        print("前置扩展内容，调用装饰器，实现一些权限校验、用户认证等功能")

        demo(*args, **kwargs)  # 使用装饰器的函数被传入到这里，使用可变参数让其更加灵活

        print("后置扩展内容，调用装饰器，实现一些日志记录、性能测试、事务处理、缓存等")

    return wrapper

# 装饰器语法糖，等价于：decorator(demo)
@decorator
def demo():
    pass

demo()
```

装饰器不仅可以放在函数上，还可以放在类上，实现一些需要使用元类才能完成的工作。还可以用来增强 Mixin。

一些特定场合，装饰器可以和 Mixin 类一起使用实现一些复杂功能

- 权限管理和日志记录

```python
# 混入类，用于提供日志记录功能
class LoggerMixin:
    def log(self, message):
        print(f"[LOG] {message}")

# 装饰器，用于权限检查
def require_permission(permission):
    def decorator(func):
        def wrapper(*args, **kwargs):
            instance = args[0]
            if hasattr(instance, 'permissions') and permission in instance.permissions:
                return func(*args, **kwargs)
            else:
                print(f"Permission '{permission}' required.")
                return None
        return wrapper
    return decorator

# 使用混入类和装饰器的主类
class User(LoggerMixin):
    def __init__(self, name, permissions):
        self.name = name
        self.permissions = permissions
    
    @require_permission('admin')
    def access_admin_panel(self):
        self.log(f"{self.name} accessed admin panel.")
        print(f"{self.name} is accessing admin panel.")

# 示例使用
admin_user = User("AdminUser", ["admin", "editor"])
admin_user.access_admin_panel()

regular_user = User("RegularUser", ["viewer"])
regular_user.access_admin_panel()
```

- 数据库操作和事务管理

```python
# 混入类，提供数据库连接
class DatabaseMixin:
    def connect(self):
        print("Connecting to database...")

# 装饰器，提供事务管理
def transactional(func):
    def wrapper(*args, **kwargs):
        instance = args[0]
        instance.connect()
        print("Starting transaction...")
        try:
            result = func(*args, **kwargs)
            print("Committing transaction...")
            return result
        except Exception as e:
            print(f"Transaction failed: {e}")
            print("Rolling back transaction...")
            return None
    return wrapper

# 使用混入类和装饰器的主类
class DataManager(DatabaseMixin):
    @transactional
    def insert_data(self, data):
        print(f"Inserting data: {data}")
        # Here would be the logic to insert data into the database
        if data == "bad data":
            raise ValueError("Invalid data")

# 示例使用
manager = DataManager()
manager.insert_data("good data")
manager.insert_data("bad data")
```

- 测试和 Mock

```python
# 混入类，提供基础测试功能
class TestMixin:
    def setUp(self):
        print("Setting up test environment...")
    
    def tearDown(self):
        print("Tearing down test environment...")

# 装饰器，模拟特定的函数行为
def mock_function(original_func):
    def decorator(func):
        def wrapper(*args, **kwargs):
            print(f"Mocking function {original_func.__name__}")
            return func(*args, **kwargs)
        return wrapper
    return decorator

# 使用混入类和装饰器的测试类
class MyTestCase(TestMixin):
    def __init__(self):
        self.setUp()
    
    @mock_function(print)
    def test_example(self):
        print("Executing test example.")

    def __del__(self):
        self.tearDown()

# 示例使用
test_case = MyTestCase()
test_case.test_example()
```
