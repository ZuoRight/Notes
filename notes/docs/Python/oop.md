# 面向对象编程

```python
class Foo():
    """
    每个定义体开头的三个双引号字符串都会被当作自我描述信息，可被__doc__以及help()等获取
    """

    # 类属性在类被加载时自动执行
    class_x = "类变量"
    __x = "私有类变量"


    # 类方法，需要参数，惯用cls
    @classmethod
    def class_method(*args):
        return *args


    """
    静态方法，无需cls或self参数
    一般用于定义一些不需要与类交互但与类紧密相关的函数，其实也可以放到类外
    """
    @staticmethod
    def static_method(*args):
        return *args


    """
    实例初始化方法，通常被称作构造器/函数，可以不定义
    类被实例化时自动执行，主要目的是在对象创建时初始化其属性或执行其他必要的设置工作
    至少要有一个self参数，self代表的就是实例化后的实例（类似其它语言中的this）
    只能默认返回None，而不能return其它值
    """
    def __init__(self, a, b):
        self.a = a  # 实例属性
        self.__b = b  # 私有实例属性
        print("类被实例化时自动执行")


    # 实例方法，实例化后调用，需要参数，惯用self
    def func(self):
        pass


    # 私有实例方法，不会被import导入
    def __func(self):
        pass
```

## 内置方法

- 类被释放时自动执行

```python
# 析构方法，内存中被释放（垃圾回收）时自动执行
# 无须自定义，但也可以在这里定义一些释放时要做的事
def __del__(self):
    print("Python自带内存分配和释放机制")
```

- 特殊方式执行

```python
# obj()带括号时执行
def __call__(self, *args, **kwargs):
    print('可以用callable(Foo)函数判断是否可执行')

# print(obj)时执行
# 如果没定义则返回：<__main__.Foo object at 0x00E90DB0>
def __str__(self):
    return '打印实例时返回的信息'

# 通常__repr__与__str__返回值一样，为了调试用的
__repr__ = __str__
```

```python
# 取值函数
# 触发方式：x = 标识符[]
def __getitem__(self, key):
    print('__getitem__',key)

# 赋值函数
# 触发方式：标识符[] = x
def __setitem__(self, key, value):
    print('__setitem__',key,value)

# 删除函数
# 触发方式：del 标识符[]
def __delitem__(self, key):
    print('__delitem__',key)


# 使类变为可迭代对象，触发方式：
# for i in obj:
#     print(i)
def __iter__(self):
    yield 1
    yield 2
    yield 3
```

## 调用

```python
# 如果当前模块名(__name__)为xxx则执行
if __name__ == "__main__":
    obj = Foo()  # 自动执行 __init__函数
    obj()  # 自动执行 __call__函数
    print(obj)  # 自动执行__str__函数

    # 访问类属性
    print(Foo.class_x)
    print(Foo._Foo__x)  # 私有
    # 访问实例属性
    # 无法访问不加self.的属性
    print(obj.a)
    print(obj._Foo__b)  # 私有

    Foo.class_method()  # 调用类方法
    Foo.static_method()  # 调用静态方法
    obj.func()  # 调用实例方法
```

- 获取成员（即类的属性和方法）

```python
    # 列出类中的所有成员
    print(Foo.__dict__)
    """
    {
    '__module__': '__main__',

    'class_x': '类变量',
    '_Foo__x': '私有类变量'
    '__doc__': '注释',

    '__init__': <function Foo.__init__ at 0x009394F8>,
    '__dict__': <attribute '__dict__' of 'Foo' objects>,
    '__weakref__': <attribute '__weakref__' of 'Foo' objects>,

    'func': <function Foo.func at 0x00939588>,
    }
    """

    # 列出对象中的所有成员
    print(obj.__dict__)
    """
    {'a': 'a', '_Foo__b': 'b'}
    """

    # 返回类/模块的描述类信息
    print(Foo.__doc__)

    # 返回当前操作的对象在属于哪个模块
    print(obj.__module__)
    """
    __main__
    """

    # 返回当前操作的对象属于哪个类
    print(obj.__class__)
    """
    <class '__main__.Foo'>
    """
```

## 继承

```python
"""
Python中一切都是对象，全部继承自object基类（也称作父类、超类）
object在Py2.x中必须写，在Py3.x可省略不写
"""
class A(object):
    def add(self, x, y):
        print(x+y)

"""
可以多重继承，按方法解析顺序查找（Method Resolution Order, MRO），但那是危险的，建议使用Mixin
获取某个类的MRO：print(Demo.mro())
子类在调用某个方法或属性的时候，首先在自己内部查找，如果没有找到，则开始根据继承机制在父类里查找
根据父类定义中的顺序，从左到右，以深度优先的方式逐一查找父类！
"""
class Demo(A, B):
    # 调用父类方法
    # super(Demo, self).add(x, y)  # Py2.x必须这样写，super(子类名, self/父类名).方法名()
    super().add(x, y)  # Py3.x可以简写
```

```python
class Parent:
    def __init__(self, a, b):
        self.a = a
        self.b = b


# 如果当前类没有定义构造器，则自动使用父类的构造器
class Child(Parent):
    pass


# 如果当前类定义了构造器，也可以使用super()方法调用父类的构造器
class Child(Parent):
    def __init__(self, a, b):
        super().__init__(a, b)


# 实例化子类时记得传入父类构造器所需的变量
child_instance = Child(1, 2)
```

## Mixin

Mixin 是一种设计模式，它用于通过继承组合多个类的行为和属性，促进代码重用，并避免常见的继承相关的问题

元编程可以用于增强 Mixin 的功能

## 结构体

假设，我们需要一个数据对象来存储一些球员的信息，可能涉及不同的数据类型，比如姓名，号码，位置等

在 C 语言中，可以创建一个结构体 `struct` 来存储不同类型的数据

```c
#include <stdio.h>
#include <string.h>

// 定义Player结构体
struct Player {
    char name[50];  // 球员姓名
    int number;  // 球员号码
    char position[20];  // 球员位置
};

int main() {
    // 创建一个Player实例并初始化
    struct Player player1;
    
    strcpy(player1.name, "Micheal Jordan");  // 给name字段赋值
    player1.number = 23;  // 给number字段赋值
    strcpy(player.position, "PG");  // 给position字段赋值

    // 打印Player实例的信息
    printf("Name: %s\n", player1.name);
    printf("Number: %d\n", player1.number);
    printf("Position: %s\n", player1.position);

    return 0;
}
```

在 Python 中，并没有内置的结构体类型，但可以使用 `tuple`, `dict`, `namedtuple`, `class`, `dataclass` 等来实现

> 虽然 Python 中有一个内置的 `struct` 模块，但主要用于处理二进制数据，与 C 的结构体完全不是一个概念

### 基础类型

缺少信息或信息类型错误等依然可以创建

- tuple

```python
jordan = ('Micheal Jordan', 23, 'PG')
jordan[2]  # 取值基于索引，很不直观
```

- dict

```python
jordan = {'name': 'Micheal Jordan', 'number': 23, 'position': 'PG'}
jordan['position']  # 无法像获取属性一样 jordan.position 取值
```

### Named Tuple

数据无法修改，无法比较

- `collections.namedtuple`

没有默认值，且不能继承

```python
from collections import namedtuple

Player = namedtuple('Player', ['name', 'number', 'position'])

jordan = Player('Micheal Jordan', 23, 'PG')
bryant = Player('Kobe Bryant', 24)  # 数据不匹配会报错

jordan.position  # 可以使用 . 语法获取属性
```

- `typing.NamedTuple`

支持默认值和方法，更加灵活

```python
from typing import NamedTuple

class Player(NamedTuple):
    name: str  # 可以加类型注解
    number: int = 0  # 可以定义默认值
    position: str

player_instance = Player("Lionel Messi", 10, "Forward")
```

### 自定义类

```python
class Player:
    def __init__(self, name, number, position):
        self.name = name
        self.number = number
        self.position = position


jordan = Player('Micheal Jordan', 23, 'PG')  # 使用位置参数创建对象
bryant = Player(name='Kobe Bryant', number=24, position='PG')  # 使用键值参数创建对象

jordan.position  # 获取属性
bryant.position = 'SF'  # 修改属性


"""
不足之处
bryant  # 对象描述不友好：<__main__.Player at 0x33426a512b7>，需要实现 __repr__ 方法来自定义描述
bryant < jordan  # 无法比较：TypeError，需要实现 __eq__, __gt__, __ge__ 等方法来实现比较
"""

# 完善代码
class Player:
    def __init__(self, name, number, position):
        pass

    def __repr__(self):
        return f'Player: \n {self.name}\t #{self.number}\t @{self.position}'

    def __eq__(self, other):
        return self.number == other.number

    def __gt__(self, other):
        return self.number > other.number

    def __ge__(self, other):
        return self.number >= other.number
```

### dataclass

`@dataclass` 相当于语法糖，简化或者说自动实现了 `__init__`, `__repr__`, `__eq__`, ... 等方法

另外数据类除了能存储数据，还可以包含方法，提供更多面向对象的特性

如果要使数据类声明后不可变，可使用 `@dataclass(frozen=True)`

```python
from dataclasses import dataclass
from typing import List, Any

@dataclass
class Player:
    name: str  # 类型注释虽然不是必须的，但强烈建议使用，如果不想限制类型可以使用Any
    number: int = 0  # 可以传默认值，但默认值不能使用可变类型(mutable)
    position: str

bryant = Player('Kobe Bryant', 24, 'PG')
james = Player('Lebron James', 23, 'SF')
james  # 描述友好，因为自动实现了__init__方法：Player(name='Lebron James', number=23, position='SF')


@dataclass
class Team:
    name: str
    players: List[Player]  # 数据嵌套

Lakers = Team('Los Angeles Lakers', [bryant, james])
Lakers  # Team(name='Los Angeles Lakers', players=[Player(name='Kobe Bryant', number=24, position='PG'), Player(name='Lebron James', number=23, position='SF')])
```
