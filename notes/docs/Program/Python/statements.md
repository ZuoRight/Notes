# 语句

## 变量命名

命名规则，大多数编程语言变量命名都只能由英文、下划线（_）、数字（不能作为开头）构成，且不能是系统保留关键字

- 下划线命名法：用于变量名、函数名、模块名等
- 驼峰命名法：主要用于类名

大小写敏感，只有少数编程语言大小写敏感，比如C，其它绝大多数都是不敏感的。

但是建议始终保持大小写一致，养成良好的编程习惯。一般全大写表示这个变量约等于常量。

```python
x = "demo"

# 获取变量内存地址
id(x)

# 获取变量所占内存大小
import sys
sys.getsizeof(x)

# 两个数学常量
import math
math.pi  # 3.141592653589793
math.e  # 2.718281828459045
```

在Python中，将一个对象赋值给一个变量，并不表示拷贝对象给变量，而是让一个变量指向了一个对象，而一个对象，可以被多个变量所指向。

另外变量可以被删除，但是对象无法被删除，只能被垃圾回收。

## 运算符

### 赋值运算符

```python
x = 2

# 同时给多个变量赋值
x,y,z = 2,3,4
x,y,z = (2,3,4)
x,y,z = [2,3,4]

# x做完算数运算后，再赋值给 x
# 类似其他语言中的递增 ++i 和递减 --i
x += 2
x -= 2
x *= 2
```

### 算术运算符

- 加 `+`

```python
1 + 2  # 3

'a' + 'b'  # 'ab'

(1,2) + (3,4)  # (1,2,3,4)
[1,2] + [3,4]  # [1,2,3,4]
```

- 减 `-`

```python
2 - 1  # 1
```

- 乘 `*`

```python
1 * 2  # 2

'~' * 2  # '~~'

(1) * 2 = (1,1)
[1] * 2 = [1,1]
```

- 求幂

```python
2**3  # 8

1.5e18  # e是指数exponent的缩写，表示：1.5 ** 18
```

- 除 `/`

```python
# 整型相除得到的是浮点型
1/1  # 1.0
10/3  # 3.3333333333333335
```

- 地板除，取整

```python
# 相除只取整数
10 // 3  # 3
```

- 取余

```python
# 相除只取余数
10 % 3  # 1
1 % 2  # 1
```

- 分组 `()`

除了分组，还可以用来代码换行，括号里的都算一行，比用反斜杠`\`更易读。

### 比较运算符

判断两个对象的值是否相等

`==`、`!=`、`>`、`>=`、`<`、`<=`

### 身份运算符

如果需要判断一个单例对象的值，比如是否为None，建议优先使用身份运算符，性能更优

`is`、`is not`

```python
a = 123
b = 123

a == b  # True
hash(a) == hash(b)  # True
id(a) == id(b)  # True，内存地址是否相等
a is b  # True

"""
出于对性能的考虑，Python内部对-5到256这些常用整数对象做了缓存处理
每次创建其实都只是引用，即id不变
"""

a = 257
b = 257

a == b  # True
hash(a) == hash(b)  # True
id(a) == id(b)  # False
a is b  # False
```

### 成员运算符

判断两个Iterabled对象的包含关系（dict用key值判断）

- `in`
- `not in`

### 布尔运算符

`and`、`or`、`not`

and和or，返回的不是bool值，而是其中的一个变量

遵循短路规则，即从左到右运算，如果通过前一个变量就能判断出结果，就不需要也不会再去判断后面的变量

- 如果前一个变量是真

```python
# and，需要知道后一个变量才能知道结果，所以返回后一个
1 and 2  # 2
1 and 0  # 0

# 真 or 一切，都是真，所以直接返回前一个
1 or 2  # 1
1 or 0  # 1
```

- 如果前一个变量是假

```python
# 假 and 一切，都是假，所以直接返回前一个
0 and 1  # 0
0 and ""  # 0

# or，需要知道后一个变量才能知道结果，所以返回后一个
0 or 1  # 1
0 or ""  # ""
```

- not 返回的是bool值，常用于if判断语句，优先级比非布尔运算符都要低

```python
not 0  # Ture
not 1  # False
```

### 位运算符

- 按位与 &

```python
# 转换为二进制后按位与
1 & 2  # 0
```

- 按位或 |

```python
# 转换为二进制后按位与
1 | 2  # 3
```

## 表达式

算数运算底层会转换为二进制做计算，而浮点数不能完美的转换为二进制，所以浮点数运算会存在精度问题，比如

```python
8.88 - 7.77  # 1.1100000000000012
```

如果有高精度要求，可以如下，不过性能会有一些损失。

```python
from decimal import Decimal

Decimal("8.88") - Decimal("7.77")  # Decimal('1.11')
```

更高级的运算可以用`math`和`cmath`函数。

## 集合的运算

```python
a = {1,2,3,4}
b = {3,4,5}

# 子集
a < b  # False
a.issubset(b)

# 并集，合并并去重
a | b  # {1,2,3,4,5}
a.union(b)


# 交集，共有元素
a & b  # {3,4}
a.intersection(b)


# 对称集，非共有元素
a ^ b  # # {1,2,5}
a.symmetric_difference(b)


# a相对于b的差集
a - b  # {1,2}
a.difference(b)

# b相对于a的差集
b - a  # {5}
b.difference(a)
```

## 判断语句

- if

```python
if condition:
    expression
[elif] condition:
    expression
[else]:
    expression
```

Python中没有`switch...case...`语句，但可以用字典来实现

<https://www.liujiangblog.com/blog/2/>

```python
def switch(key):
    case = {
        1: "1",
        2: "2",
        3: "3"
    }

    v = case.get(key, "0")
    return v

switch(1)  # "1"
switch(8)  # "0"
```

```python
if x > y:
    res = x
else:
    res = y

# 不换行
if x > y: res = x
else: res = y

# 三元表达式
res = x if x > y else y
res = 1 if 0 else 2 if False else 3  # 3
```

## 循环语句

循环过程中如果改变了对象长度，则可能会影响循环次数，比如循环一个长度为2列表：`for i in _list`，第一次循环中移除了一个元素，此时列表长度变为1，则不会进入下一次循环

这时候可以这样去循环：`for i in list(_list)`

- for

> 常用于循环次数限定的时候
>
> 嵌套循环，外层主要用于循环次数，内层用于遍历

```python
for item in iterable:
    expression
# 正常结束循环时，else子句执行
else:
    expression
```

- while

> 常用于循环次数限定的时候

比 for 循环自由度更高，因为 for 循环需要依次遍历，而 while 可以根据条件不依次遍历

```python
while condition:
    expression
# while也可以接else
else:
    expression
```

## 控制循环

- pass

用于占位

- continue

中止当前循环，继续执行下一次循环

- break

终止整个循环，但只能结束当前层循环

如果想结束所有循环（类似c语言中的goto语句），可以在最外层设置一个flag，让内部控制

```python
# 设置一个flag，默认为False
flag = False

for i in range(10):
    if flag:
        break  # 跳出外部循环

    for j in range(10):
        if j==7:
            flag = True
            break  # 跳出内部循环
        print(i,j)
```

## with 语句

上下文管理器（Context Manager）是指在一段代码执行之前执行一段代码，用于一些预处理工作。执行之后再执行一段代码，用于一些清理工作。

比如打开文件进行读写，读写完之后需要将文件关闭。又比如在数据库操作中，操作之前需要连接数据库，操作之后需要关闭数据库。

上下文管理器之所以能够实现上述两个功能，是因为定义了上下文管理协议中的两个方法：`__enter__`和`__exit__`。

```python
# 自定义打开文件操作的上下文管理器
class MyOpen(object):
    def __init__(self, file_name):
        """初始化方法"""
        self.file_name = file_name
        self.file_handler = None
        return

    def __enter__(self):
        """enter方法，返回file_handler"""
        print("enter:", self.file_name)
        self.file_handler = open(self.file_name, "r")
        return self.file_handler

    def __exit__(self, exc_type, exc_val, exc_tb):
        """exit方法，关闭文件并返回True"""
        print("exit:", exc_type, exc_val, exc_tb)
        if self.file_handler:
            self.file_handler.close()
        return True

# 另外还可以使用装饰器实现上下文管理器
@contextlib.contextmanager
def open_func(file_name):
    # yield前为__enter__方法
    print('open file:', file_name, 'in __enter__')
    file_handler = open(file_name, 'r')

    yield file_handler

    # yield后为__exit__方法
    print('close file:', file_name, 'in __exit__')
    file_handler.close()
    return
```

- 使用with语句即可调用上下文管理器

```python
with MyOpen("python_base.py") as file_in:
    """
    1. 执行语句，获取上下文管理器
    2. 调用上下文管理器中的__enter__方法，该方法执行一些预处理工作
    3. 将__enter__方法的返回值赋值给file_in，也可以不赋
    4. 执行以下代码块，file_in可以被当作普通变量使用
    """
    for line in file_in:
        print(line)
        raise ZeroDivisionError
    """
    5. 调用上下文管理器中的的__exit__方法
        __exit__方法有三个参数：exc_type, exc_val, exc_tb。
            如果上面代码块发生异常并退出，那么分别对应异常的type、value 和 traceback。
            否则三个参数全为None。
        __exit__方法的返回值可以为True或者False
            如果为True，那么表示异常被忽视，相当于进行了try-except操作（此示例正是这种情况）
            如果为False，则该异常会被抛出
    """
```

- 可以嵌套

```python
with A() as a, B() as b:
    SUITE

# 等价于

with A() as a:
    with B() as b:
        SUITE
```
