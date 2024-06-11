# 面向对象编程

面向对象编程的四个特性：封装、抽象、继承、多态

## 封装 Encapsulation

封装就是将数据（属性）和代码（方法）绑定到单一的单位（类）中，并限制对某些组件的直接访问

### 限制访问

- 私有的

以 `__` 开头的实例属性或方法，只能由类本身访问，不能被外部类或子类访问

> 注意：方法中的变量前使用 `__` 是无效的，只对类或实例属性生效

会触发名称改编（name mangling）的过程，比如 `__name` 会被改编为 `_ClassName__name`

也就是说，只不过换了个名称，其实还是可以访问到的，但是不推荐

- 受保护的

以 `_` 开头的属性或方法被视为受保护的，只应在类本身及其子类中使用，不过这只是一种约定俗成，实际还是可以访问到的

```python
class MyClass:
    """
    每个定义体开头的三个双引号字符串都会被当作自我描述信息
    可被 __doc__ 以及 help() 等获取
    """

    # 类属性，当类被加载时即生效
    # 类属性会被所有实例共享，无论公有的还是私有的
    __private_class_var = "私有类属性"
    _class_var = "受保护的类属性"
    class_var = "正常类属性"


    """
    初始化方法，通常被称作构造器，可选
    类被实例化时自动执行，主要目的是在对象创建时初始化其属性或执行其他必要的设置工作
    至少要有一个self参数，self代表的就是实例化后的实例（类似其它语言中的this）
    只能默认返回None，而不能return其它值
    """
    def __init__(self):
        # 实例属性，每个实例都有独立的副本
        self.__private_var = "I am private"
        self._protected_var = "I am protected"


    # 私有方法不会被import导入
    def __private_method(self):
        return "This is a private method"


    def _protected_method(self):
        return "This is a protected method"

    
    # 实例方法，实例化后调用，需要参数，惯用self
    def func(self):
        pass

    
    # 类方法，需要参数，惯用cls
    @classmethod
    def method_class(*args):
        return *args


    """
    静态方法，无需cls或self参数
    一般用于定义一些不需要与类交互但与类紧密相关的函数，其实也可以放到类外
    """
    @staticmethod
    def method_static(*args):
        return *args


obj = MyClass()

# 访问私有变量
print(obj.__private_var)  # 会抛出 AttributeError
print(obj._MyClass__private_var)  # 非标准访问是可以的


# 全局变量
__global_private = "Global private"  # 不会变为私有的，这样写没意义
_global_protected = "Global protected"  # 但可以是受保护的


def function():
    # 方法前的双下划线没有特殊影响，这样写没意义
    __local_in_function = "Local in function"
```

### 内置方法

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
# 取值函数，触发方式：x = 标识符[]
def __getitem__(self, key):
    print('__getitem__',key)

# 赋值函数，触发方式：标识符[] = x
def __setitem__(self, key, value):
    print('__setitem__',key,value)

# 删除函数，触发方式：del 标识符[]
def __delitem__(self, key):
    print('__delitem__',key)


# 使类变为可迭代对象，触发方式：for i in obj: print(i)
def __iter__(self):
    yield 1
    yield 2
    yield 3
```

### 调用

```python
# 如果文件作为脚本执行时
if __name__ == "__main__":
    # 访问类属性
    print(Foo.x_class)
    print(Foo._Foo__x)  # 私有

    Foo.method_class()  # 调用类方法
    Foo.method_static()  # 调用静态方法

    obj = Foo()  # 自动执行 __init__函数
    obj()  # 自动执行 __call__函数
    print(obj)  # 自动执行__str__函数

    # 访问实例属性，无法访问不加self.的属性
    print(obj.a)
    print(obj._Foo__b)  # 私有

    obj.func()  # 调用实例方法
```

- 获取成员（即类的属性和方法）

```python
    # 列出类中的所有成员
    print(Foo.__dict__)
    """
    {
    '__module__': '__main__',

    'x_class': '类变量',
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

## 抽象 Abstraction

抽象是隐藏复杂性只暴露必要功能的过程。

抽象类不能被实例化，只能被其他类继承。

抽象方法定义在抽象类中，必须在继承抽象类的子类中实现。

```python
from abc import ABC, abstractmethod

class Vehicle(ABC):
    @abstractmethod
    def move(self):
        pass

class Car(Vehicle):
    def move(self):
        print("The car is moving")
```

## 多态 Polymorphism

多态是指相同的函数或方法在不同的对象上可以有不同的实现。在 Python中，它是通过简单地定义具有相同名称的多个方法，或者通过重写继承的方法来实现的。

```python
class Animal:
    def speak(self):
        pass

class Dog(Animal):
    def speak(self):
        print("Bark")

class Cat(Animal):
    def speak(self):
        print("Meow")

def animal_sound(animal):
    animal.speak()  # 调用实际传入对象的speak方法

dog = Dog()
cat = Cat()

animal_sound(dog)  # 输出 Bark
animal_sound(cat)  # 输出 Meow
```

## 继承 Inheritance

```python
"""
Python 中一切皆对象，全部继承自 object 基类（也称作父类、超类）
object 在 Py2.x 中必须写，在 Py3.x 可省略不写
"""
class Parent:
    def __init__(self, a, b):
        self.a = a
        self.b = b


# 如果当前类没有定义__init__，则自动使用父类的构造器
class Child(Parent):
    pass


# 如果当前类定义了__init__，也可以使用super()方法调用父类的构造器
class Child(Parent):
    def __init__(self, a, b):
        super().__init__(a, b)

    # 调用父类方法，Py2.x 必须这样写，super(子类名, self/父类名).方法名()
    super().add(x, y)  # Py3.x可以简写


# 实例化子类时记得传入父类__init__()所需的变量
child_instance = Child(1, 2)
```

- 多重继承

多重继承的方法解析顺序遵循称为C3线性化或C3超类线性化的算法，然后得到的类及其父类的顺序列表被称作 MRO(Method Resolution Order)，Python 通过这个顺序来解析一个类的属性和方法调用

```python
class A:
    pass

class B(A):
    pass

class C(A):
    pass

class D(B, C):
    pass


"""
1. 先从左到右，B > C，所以先调用 B
2. 然后调用 B 的父类 A
3. 然后调用 C 以及 C 的父类
4. 由于 A 是 B 和 C 的共同父类，所以 A 要在 C 后被调用

则最终MRO顺序为：D -> B -> C -> A -> object
"""


# 查看调用顺序，也可以用 D.__mro__
D.mro()  # [<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>
```

## 混入类

如果一个类的作用是为多个不相关的子类提供方法，实现复用但不体现“是什么”的关系，应该把这个类明确地定义为混入类。

Python 中没有声明混入类的正式方式，只是约定俗成在名称中加入 `Mixin` 后缀

特点

- 单一功能：通常只提供一组相关的功能，而不是代表一个实体。
- 无独立实例：绝不应被实例化，只应被多重继承，即一个具体类不应该只继承混入类。
- 无状态：通常不存储状态，只提供方法，即避免定义`__init__()`以及属性。

```python
# 提供日志记录功能
class LoggerMixin:
    def log(self, message):
        """Simple method to log messages"""
        print(f"[LOG] {message}")

class Worker:
    def __init__(self, name):
        self.name = name
    
    def work(self):
        print(f"{self.name} is working.")

# Using LoggerMixin to add logging functionality to Worker
class LoggedWorker(Worker, LoggerMixin):
    def __init__(self, name):
        super().__init__(name)

    def work(self):
        self.log(f"{self.name} started working.")
        super().work()
        self.log(f"{self.name} finished working.")

# Example usage
worker = LoggedWorker("Alice")
worker.work()
```
