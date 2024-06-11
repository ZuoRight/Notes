# 数据类型

标准库：<https://docs.python.org/zh-cn/3.10/library/index.html>

## 原子类型

### int 整数

```python
# 没有大小限制，inf表示无限大
x = 10  # 默认十进制，10

x = 0b10  # 二进制，2
x = 0o10  # 八进制，8
x = 0x10 # 十六进制，16
```

### float 浮点数

精度默认与其他语言的双精度一样

```python
x = 0.0001
x = 1e-04  # 用科学计数法表示
```

### complex 复数

```python
# 复数由实部和虚部组成，实部和虚部都是浮点型
# 在数学规范中虚部常用i表示，但Python遵循的电气学规范常用j表示
x = 1 + 2j
# 或者complex(real, imag)函数表示
y = complex(2, -3)

print(x)  # (1+2j)
print(x.real)  # 打印实部，1.0
print(x.imag)  # 打印虚部，2.0
print(x.conjugate()) # 打印共轭复数（实部相等，虚部相反），(1-2j)
print(x + y)  # (3-1j)
```

### Fraction 有理数（分数）

```python
from fractions import Fraction

# 创建一个Fraction对象
frac = Fraction(3, 2)
print(frac)  # 输出: 1.5
print(frac.numerator)  # 输出分子: 3
print(frac.denominator)  # 输出分母: 2
```

### bool 布尔值

```python
bool(1)  # True

bool(0)  # False
bool(None)  # False
bool('')  # False
```

### NoneType 无（未知）

只有这一个单例对象：`None`

## 序列

### bytes

通常用于网络数据传输、二进制图片和文件的保存等等。

- 前缀b

```python
_bytes = b''
_bytes = bytes()
_bytes = b'\xe4\xb8\xad\xe6\x96\x87'  # "中文"
_bytes = b'6\xb6a'  # 三个字节：6、\xb6、a
```

### string

```python
_str = ""
_str = "a"
_str = 'abc'  # 单引号
_str = "abc"  # # 双引号
_str = '''abc'''  # 多行字符串
```

- 前缀r

```python
# 取消转义，输出原始字符
# 常用于正则表达式，文件路径等地方
x = r'\n'
```

- 前缀u

```python
# 表示存储时使用unicode编码
# Py2默认为ascii编码，中文字符前需要加前缀u
# Py3默认为utf_8编码，所以不用加前缀u
x = u'中文'
```

- 前缀f

```python
# Py3.6新特性，可以用于格式化字符串
b = "b"
abc = f'a{_str}c'  # "abc"
```

### tuple

元组的存储空间固定，比列表更节省空间，初始化相同元素时元组要快5倍(linux系统)，所以若要存储固定的数据，优先考虑使用元组

```python
# 创建空元组
_tuple = ()
# 或者用构造器
_tuple = tuple()

_tuple = ("v",)  # 单元素时需要加逗号，不然会被认为是字符串

_tuple = ("v1", "v2", "v3")
_tuple = "v1", "v2", "v3"  # 可以不带括号

_tuple = ("v1", "v2", ("v3", "v4"))

_tuple = ("v1", "v2", ["v3", "v4"])

_tuple = ("v1", "v2", {"v3":3, "v4":4})
```

### list

```python
# 创建空列表
_list = []
# 或
_list = list()

_list = ["v"]

_list = ["v1", "v2", "v3"]

_list = ["v1", "v2", ("v3", "v4")]

_list = ["v1", "v2", ["v3", "v4"]]

_list = ["v1", "v2", {"v3":3, "v4":4}]
```

## 散列

### set

集合中的元素必须为可哈希的，即不允许重复元素，所以很适合用于去重以及执行数学上的集合操作如并集、交集、差集和对称差集等。

```python
_set = set()  # 空set，只能用构造器创建
_set = {"k1", "k2", "k3"}
```

不可变集合：`frozenset( )`，即创建后不可以添加删除修改元素，与 tuple 的区别是不可重复

### dict

虽然 `None`、`True`、`False` 也是可哈希的，但一般不用作 key

```python
# 创建空字典
_dict = {}
# 或
_dict = dict()

_dict = {1:"a"}

_dict = {"k1":"v1", "k2":"v2", "k3":"v3"}

# 使用dict()构建字典时，key值不能加引号
_dict = dict(k1="v1", k2="v2")

# 字典的key值只能为可哈希类型
_dict = {("k1", "k2"): "v1"}

# 字典的value值，与列表一样，可以是任意类型
_dict = {"k1":(1,2), "k2":[1,2], "k3":{1,2}, "k4":{"v4": 3}}
```

## range()

`range(start, stop, step)`

与切片等一样，不包含右边界

```python
# 生成整数列表的迭代器，需要使用list()转换为列表
# start默认为0，step默认为1，生成的列表不包含stop
list(range(5))  # 等价于range(0,5,1)，[0,1,2,3,4]
list(range(0))  # []
list(range(0,-5,-1))  # [0,-1,-2,-3,-4]
```

## 推导式

### list推导式：[]

`[expression for item in iterable]`

也可以嵌套循环

```python
# 生成[0x0, 1x1, 2x2, 3x3...]
_list = [i**2 for i in range(5)]  # [0, 1, 4, 9, 16]

# 等价于
_list = []
for i in range(5):
    i = i**2
    _list.append(i)
```

后面可以跟if条件筛选，但不能带else。

`[expression for item in iterable if condition]`

```python
# 生成[0x0, 2x2, 4x4...]
_list = [i**2 for i in range(5) if i%2==0]  # [0, 4, 16]
```

表达式中也可以包含if判断，但必须加else。

`[expression if condition else for item in iterable]`

```python
_list = [i if i<"b" else i.upper() for i in ["a","b","c"]]  # ['a', 'B', 'C']
```

可以包含多层循环。一般就两层，内层在前面，外层在后面

`[expression for item1 in iterable1 for item2 in iterable2]`

```python
_list4 = [x+y for x in ["a","b"] for y in ["A","B"]]  # ['aA', 'aB', 'bA', 'bB']
```

### dict/set推导式：{}

```python
_dict={"a":1, "b":2, "c":3}
# 反转dict的键和值
_tcid = {v:k for k,v in _dict.items()}  # {1: 'a', 2: 'b', 3: 'c'}

# 取dict的键组成一个集合
_k = {k for k,v in _dict.items()}  # {'c', 'a', 'b'}
```

## 迭代器

```python
# 可以使用 next() 函数逐个获取元素，直到抛出 StopIteration 异常
r = next(iterator)

# 遍历迭代器
r = [i for i in iterator]

# 将迭代器转为list
list(iterator)
```

### 自定义迭代器

实现 `__next__()` 方法

### 生成器

- 生成器表达式

```python
g = (x for x in range(5))
```

- 生成器函数

斐波那契数列

```python
def fib(max):
    n = 0
    a, b = 0, 1
    while n < max:
        if (counter > n): 
            return  # 函数执行完return语句就结束了
        yield b  # 但函数执行完yield语句只是暂停
        # 执行到此暂停，对象存储到迭代器数据流中
        # 调用一次next()，将生成器对象返回，然后继续执行后面的代码，循环到此，再一次暂停
        a, b = b, a + b
        n = n + 1

g = fib(5)  # <generator object demo at 0x100d55a50>
next(g)  # 获取生成器对象的值，['...']
```

消费者-生产者

```python
# 消费者
def consumer():
    message = "start"
    while True:
        receiver = yield message  # yield语句执行完，返回message值，然后暂停，可以接收send发送的消息，下一轮启动然后赋值给receiver
        print("receiver: ", receiver)  # 等待下一次启动才会执行赋值语句
        if not receiver:
            return "end"
        message = "200 OK"  # 执行完这里，会继续返回执行 yield message，也就是说此函数每一轮返回的都是message的值


if __name__ == '__main__':
    gen = consumer()  # <generator object consumer at 0x100d55a50>
    print(next(gen))  # 预激
    """
    在一个生成器函数未启动之前，是不能传递值进去
    也就是说在使用gen.send(x)之前，必须先使用 next(gen) 或者 gen.send(None) 来返回生成器的第一个值
    send() 类似 next()，但可以传递参数，发送消息：gen.send("message")
    """
    # 模拟生产者
    print(gen.send(1))  # 200 OK
    print(gen.send(2))  # 200 OK
    print(gen.send(3))  # 200 OK
    print(gen.send(None))  # StopIteration: end
```

### 内置函数

- `iter(iterable)`

```python
iter("123")  # <str_iterator object at 0x10de4aa10>
iter((1,2,3))  # <tuple_iterator object at 0x10de4a950>
iter([1,2,3])  # <list_iterator object at 0x10de4aa10>
iter({1,2,3})  # <set_iterator object at 0x10de4bf50>
iter({"a":1,"b":2})  # <dict_keyiterator object at 0x10de469b0>
```

- `filter(function, iterable)`

序列的每个元素作为参数传递给一个返回 `True` 或 `False` 的函数进行判断，将返回 `True` 的所有元素组成一个新的可迭代对象 `filter object`。

```python
def is_even(n):
    return n % 2 == 0

numbers = [1, 2, 3, 4, 5, 6]
filtered_numbers = filter(is_even, numbers)
list(filtered_numbers)  # [2, 4, 6]
```

- `map(function, iterable)`

遍历可迭代对象，将每个元素都作为参数传给函数，最后返回一个新的可迭代对象

```python
def square(x):
    return x ** 2

numbers = [1, 2, 3, 4, 5, 6]
m = map(square, numbers)  # 计算列表各个元素的平方，返回迭代器：<map object at 0x100d3d550>     
list(m)  # [1, 4, 9, 16, 25, 36]

# 通常使用lambda匿名函数来简化以上写法
map(lambda x: x ** 2, numbers)
```
