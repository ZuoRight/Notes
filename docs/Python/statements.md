# 语句

## 算术运算

算数运算底层会转换为二进制做计算，而浮点数不能完美的转换为二进制，所以浮点数运算会存在精度问题，比如

```python
8.88 - 7.77  # 1.1100000000000012
```

如果有高精度要求，可以如下，不过性能会有一些损失。

```python
from decimal import Decimal

Decimal("8.88") - Decimal("7.77")  # Decimal('1.11')
```

更高级的运算可以用 [`math`](https://docs.python.org/zh-cn/3.9/library/math.html) 和 `cmath` 函数。

### 加 `+`

```python
1 + 2  # 3

'a' + 'b'  # 'ab'

(1,2) + (3,4)  # (1,2,3,4)
[1,2] + [3,4]  # [1,2,3,4]
```

### 减 `-`

```python
2 - 1  # 1
```

### 乘 `*`

```python
1 * 2  # 2

'~' * 2  # '~~'

(1) * 2 = (1,1)
[1] * 2 = [1,1]
```

### 除

- 真除法 True Division，`/`

结果都是浮点型，即使两个整型相除得到的也是浮点数

```python
1 / 1  # 1.0
10 / 3  # 3.3333333333333335
```

- 取整，也叫地板除 Floor Division，`//`

常用于分页计算（需要整数）、负数取整（向下取整）

```python
10 // 3  # 3
10.0 // 3  # 3.0
-5 // 2  # -3（向负无穷取整）
```

- 取余，也叫取模 Modulo Operator，`%`

$a\%b$ 的结果满足以下数学关系：$a=b×(a//b)+(a\%b)$

```python
10 % 3  # 1 整数
1.0 % 2  # 1.0 浮点数

# 符合与除数相同
7 % 3  # 7 = 3*2 + 1，所以结果是 1
-7 % -3	# -7 = -3*2 + (-1)，所以结果是 -1
# 负数取余
-7 % 3  # -7 = 3*(-3) + 2，所以结果是 2
7 % -3  # 7 = -3*(-3) + (-2).所以结果是 -2
```

### 求幂

```python
2**3  # 8

1.5e18  # e是指数exponent的缩写，表示：1.5 ** 18
```

### `()`

括号的用途取决于不同的上下文

比如用于分组、当作调用运算符、还可以用来代码换行，括号里的都算一行，比用反斜杠 `\` 更易读。

## 其它运算

### 比较运算

判断两个对象的值是否相等

`==`、`!=`、`>`、`>=`、`<`、`<=`

比较版本

```python
import sys

print(sys.version_info)
# 输出: sys.version_info(major=3, minor=10, micro=8, releaselevel='final', serial=0)

# 使用元组比较
print(sys.version_info >= (3, 10))  # 如果是 3.10.8，输出: True
print(sys.version_info >= (3, 10, 0))  # 更精确的比较
```

### 身份运算

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

### 成员运算

判断两个 Iterabled 对象的包含关系（dict 用 key 值判断）

- `in`
- `not in`

### 布尔运算

`and`、`or`、`not`

and 和 or，返回的不是 bool 值，而是其中的一个变量

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

### 位运算

- 按位与 `&`

```python
# 转换为二进制后按位与
1 & 2  # 0
```

- 按位或 `|`

```python
# 转换为二进制后按位与
1 | 2  # 3
```

### 集合的运算

```python
a = {1, 2, 3, 4}
b = {3, 4, 5}

# 交集，共有元素
a & b  # {3,4}
a.intersection(b)

# 并集，合并并去重
a | b  # {1,2,3,4,5}
a.union(b)

# 子集
a < b  # False
a.issubset(b)

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

## 赋值语句

- 普通赋值

```python
x = 2
```

- 自增自减

```python
# x 做完算数运算后，再赋值给 x，类似其他语言中的递增 ++i 和递减 --i
x += 2
x -= 2
x *= 2
```

- 并行赋值

```python
# 同时给多个变量赋值，要一一对应，否则会报错
x, y, z = 2, 3, 4

# 同时给多个变量赋相同值
x = y = z = 1
```

- 交换赋值

```python
a, b = 5, 10
a, b = b, a
```

- 解包

```python
x,y,z = (2,3,4)  # 元组解包
x,y,z = [2,3,4]  # 列表解包

# 解包并捕获多余的值
numbers = [1, 2, 3, 4, 5]
x, y, *rest = numbers
print(rest)  # [3, 4, 5]
```

## 判断语句

### if

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
```

### 三元表达式

```python
res = x if x > y else y
res = 1 if 0 else 2 if False else 3  # 3
```

```python
_s = "a,bb,ccc"
res = a.split(",") if a else []
# 另一种写法
res = a and a.split(",") or []
```

## 循环语句

循环过程中如果改变了对象长度，则可能会影响循环次数，比如循环一个长度为2列表：`for i in _list`，第一次循环中移除了一个元素，此时列表长度变为1，则不会进入下一次循环，这时候可以这样去循环：`for i in list(_list)`

for 循环正常只能依次遍历，而 while 可以根据条件不依次遍历，自由度更高

嵌套循环，外层主要控制循环次数，内层用于遍历

### for

```python
for item in iterable:
    expression
else:
    expression
```

for 循环后可以接 else 语句，正常结束循环时执行，但如果 break 打断循环则不执行

```python
for i in range(10):
  if i == 5:
    break  # 终止循环，且不会执行 else 语句
else:
  print("循环完成")
```

### while

```python
while condition:
    expression
# while也可以接else
else:
    expression
```

## 跳转语句

### continue

中止当前循环，继续执行下一次循环

### break

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

### return

## 空操作语句

`pass` 常用于占位

## 异常处理语句

### try

```python
try:
    pass  # 可能会出错的地方
except Xxx1Error as e1:
    print(e1)
except Xxx2Error as e2:
    print(e2)
except (Xxx3Error,Xxx4Error) as es:
    print("可以同时捕获多个异常")
except Exception as e:
    print('通用异常，可匹配任意异常')
except:
    print("如果不写异常名称，将匹配所有异常")
else:
    print("如果没有异常则执行")
finally:
    print("不管有没有异常最后都会执行")
```

### raise

抛出异常

### assert

断言

```python
assert n != 0, "n is zero"  # n如果等于0则抛出次错误提示

assert A and B and C  # 会从做到有断言，只要断言失败，就不会再继续向右继续断言
# 但是更建议分开写
```
