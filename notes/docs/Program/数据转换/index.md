# 引言

程序运行的过程中，所有的变量都是在内存中临时存储，把内存中的变量变为可存储或者可传输的，然后才能存储到磁盘或者数据交换，这个过程称之为序列化，反之，称为反序列化

- dump 存/写 序列化
- load 取/读 反序列化

## 关于编码

> <https://docs.python.org/3/library/codecs.html#standard-encodings>

Python3 默认为 `utf_8` 编码，可忽略大小写，还可用 `_` 替代 `-`，所以这些别名也都有效：`utf-8`, `utf8`, `UTF-8`, `U8`, `UTF`, `cp65001`，但使用别名可能会导致执行速度变慢

## 文件读写

使用Python的内置函数 `open()` 来操作文件

> 文档：<https://docs.python.org/zh-cn/3.10/library/functions.html#open>

- 打开文件，返回一个文件对象
- 进行读写操作
- 关闭文件

```python
f = open(filename, mode="r", encoding='utf_8', errors='ignore')
"""
mode 操作模式
encoding 操作非utf_8编码文件时需要指定编码格式
errors 如果夹杂非法编码字符，默认忽略处理，也可以自定义
"""

# 读操作
f.read()  # 读取全部
f.read(size)  # 读取指定字符
f.readlines()  # 读取全部，按行存入list

# readlines会把每行后面的回车"\n"也读到，可以这样处理
for line in f.readlines():
    print(line.strip())

# 写操作
f.write()  # 写入

# 关闭文件，防止文件泄露或破坏
f.close()  
```

支持 with 语句触发上下文管理器，自动关闭，自动处理异常

```python
with open(filename1, mode) as f:
    f.read()
```

### 操作模式

操作

- `r` 只读（默认），文件不存在则报错
- `x` 新建，文件不存在则新建，存在则报错
- `w` 只写，文件不存在则新建，存在则清空
- `a` 追加，文件不存在则新建，存在则追加

类型

- `t` 文本模式（默认），可省略，`r` 与 `rt`等价
- `b` 二进制模式，读写bytes数据，比如Excel、图片、视频等，`rb`、`wb`、`ab`

> office文件其实是个压缩包，由很多.xml和其他文件构成，所以属于二进制

更新/读写模式，较少使用

- `r+`、`rb+`、`w+`、`a+`

## 内存读写

### StringIO

- 向内存中写入str数据

```python
from io import StringIO
# 创建一个缓存对象
f = StringIO()

f.write('Hello!\nHi!\nGoodbye!')  # 像操作文件一样写入
```

- 从内存中读取str数据

```python
from io import StringIO
# 创建一个缓存对象
f = StringIO('Hello!\nHi!\nGoodbye!')  # 初始化

f.read()  # 像操作文件一样读取
```

### BytesIO

- 向内存中写入bytes数据

```python
from io import BytesIO
# 创建一个缓存对象
f = BytesIO()

f.write(b'\xe4\xb8\xad\xe6\x96\x87')  # 像操作文件一样写入
```

- 从内存中读取bytes数据

```python
from io import BytesIO
# 创建一个缓存对象
output_buffer = BytesIO(b'\xe4\xb8\xad\xe6\x96\x87')  # 初始化

f.read()  # 像操作文件一样读取
```

### 指针问题

- `f.tell()`  返回指针当前位置
- `f.seek(offset, 0)`  移动指针位置，从初始位置开始计算（默认），移动offset个字节
- `f.seek(7, 1)`  移动指针位置，从当前位置开始计算，往后移动7个字节
- `f.seek(-7, 2)`  移动指针位置，从末尾位置开始计算，往前移动7个字节

```python
print(f.tell())  # 操作前，指针位置是0

f.write('Hello')  # 进行读取/写入操作
print(f.tell())  # 操作后，指针位置移到了最后5

print(f.read())  # 此时如果读取，则会得到空值
f.seek(0)  # 可以将指针移回初始位置，此时再读取将可以读到
```

```python
# 另外也可以用以下方式获取，不受指针位置影响
print(f.getvalue())
```
