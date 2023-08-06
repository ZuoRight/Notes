# 类

```python
class Foo():
    """
    每个定义体开头的三个双引号字符串都会被当作自我描述信息，可被__doc__以及help()等获取
    """

    print("类属性在类被加载时自动执行")
    class_x = "类变量"
    __x = "私有类变量"

    """
    类方法，需要参数，惯用cls
    """
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
    实例初始化方法，有些语言也叫构造函数，类被实例化时自动执行
    至少要有一个self参数，self代表的就是实例化后的实例（类似其它语言中的this）
    只能默认返回None，而不能return其它值
    """
    def __init__(self, a, b):
        self.a = a  # 实例属性
        self.__b = b  # 私有实例属性
        print("类被实例化时自动执行")

    """实例方法，实例化后调用，需要参数，惯用self"""
    def func(self):
        pass

    """私有实例方法，不会被import导入"""
    def __func(self):
        pass
```

## 内置方法

- 类被释放时自动执行

```python
    """
    析构方法，内存中被释放（垃圾回收）时自动执行
    无须自定义，但也可以在这里定义一些释放时要做的事
    """
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

    """
    使类变为可迭代对象，触发方式：
    for i in obj:
        print(i)
    """
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
"""
class Demo(A, B):
    # 调用父类方法
    """
    获取某个类的MRO：print(Demo.mro())
    子类在调用某个方法或属性的时候，首先在自己内部查找，如果没有找到，则开始根据继承机制在父类里查找
    根据父类定义中的顺序，从左到右，以深度优先的方式逐一查找父类！
    """
    super(Demo, self).add(x, y)  # Py2.x必须这样写，super(子类名, self/父类名).方法名()
    super().add(x, y)  # Py3.x可以简写
```
