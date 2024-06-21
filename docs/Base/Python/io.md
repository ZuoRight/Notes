
# I/O 操作

I/O 操作包括从内存、文件、网络、设备等不同数据源读取或写入数据

## 文件读写

使用 Python 的内置函数 `open()` 来操作文件

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

## Pickle

Python 特有的一种数据格式，用于将 Python 对象序列化为二进制格式，以便存储到文件或传输。

- 写

```python
import pickle

# 存到内存
y = pickle.dumps(x)

# 存储到文件，必须是“wb”模式
with open("test.pickle", "wb") as f:
    pickle.dump(x, f)
```

- 读

```python
import pickle

# 从内存中取出
pickle.loads(y)

# 从文件中取出，必须是“rb”模式
with open("test.pickle", "rb") as f:
    pickle.load(f)
```
