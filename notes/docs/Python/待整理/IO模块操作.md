# IO模块

因为现代操作系统不允许普通的程序直接操作磁盘  
所以在磁盘上读写文件的功能都是由操作系统提供的接口实现的

Input：输入进CPU/内存，读操作  
Output：输出到磁盘/网络等外设，写操作

## File 对象的读写操作

### **1. 请求操作系统，打开一个文件对象（通常称为文件描述符）**

```python
f = open('文件路径', '打开模式', encoding='UTF-8'（默认，指定编码）, errors='ignore'（默认，忽略编码错误）)
```

读模式打开

- "r"，只读（默认）
- “rb”，读取二进制文件

写模式打开

- “w”，只写
- “wb”，写入二进制文件
- “a”，追加写入

### **2. 操作文件：读或写**

读操作

- `f.read()`，读取全部
- `f.read(size)`，读取部分
- `f.readline()`，读取一行
- `f.readlines()`，读取所有，但按行返回list

写操作

- `f.write()`

### **3. 关闭文件：f.close()**

---

## File-like 对象的读写操作

除了文件外（包括文本文件和二进制文件）  
还可以打开类文件对象进行读写操作，比如缓存

StringIO：操作缓存中的str数据

```python
from io import StringIO
output_buffer = StringIO()
output_buffer = StringIO('Hello!\nHi!\nGoodbye!')  # 还可以初始化
```

BytesIO：操作缓存中的二进制数据

```python
from io import BytesIO
output_buffer = BytesIO()
output_buffer = BytesIO(b'\xe4\xb8\xad\xe6\x96\x87')  # 还可以初始化
```

获取缓存中的数据

```python
print(f.getvalue())
```

---

## 错误处理

操作不存在的文件时会报IOError错误，可使用`try...finally`预处理，但每次这样很麻烦，可使用with语句自动处理。

```python
with open("路径", "打开模式") as f:
    print(f.read()) # 读
    # 或
    f.write() # 写
```

---

## 同步和异步

由于CPU/内存的处理速度远大于外设，即存在速度不匹配的问题  
> 同步：CPU/内存处理完要等待外设处理  
> 异步：CPU/内存处理完不等待外设处理，继续去处理别的事，得到通知后再回来继续处理
>> 回调模式：服务员跑过来通知你
>> 轮询模式：服务员发短信通知你，得不停检查是否发了短信给你
