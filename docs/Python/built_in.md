# 内置函数和标准库

## 部分内置函数解析

- `help()` 查看内置模块的使用帮助
- `abs(x)` 求绝对值
- `round(x, n)` 四舍五入

```python
round(1.237, 2)  # 1.24
```

- `slice()`、`reversed()`、`zip()`

```python
_str = "abc"
_tuple = (1,2,3)
_list = [1,2,3]


# slice()
_list[slice(0,2)]  # 等价于：_list[0:2]


# reversed()
# 反转后得到一个迭代器
r = reversed(x)  # <reversed object at 0x105acab50>
[i for i in r]  # [3,2,1]


# zip()
r = zip(_tuple,_list)  # <zip object at 0x10777b550>
[i for i in r]  # [(1, 1), (2, 2), (3, 3)]
```

- `enumerate()`

```python
lst = ['one','two','three','four','five']
[i for i in enumerate(lst)]  # [(0, 'one'), (1, 'two'), (2, 'three'), (3, 'four'), (4, 'five')]
[i for i in enumerate(lst, 1)]  # 标号从1开始
```

- `all()`、`any()`

```python
all(Iterable)  # Iterable元素中全是True，则返回True
any(Iterable)  # Iterable元素中有一个是True，则返回True
```

- `input()`、`print()`

```python
r = input("请输入...")  # 返回str类型


# 注意：
# 1. 同时输出多个值，默认空格分隔，可以自定义seq变量控制
# 2. 输出结束会末尾会自动加一个换行，可以自定义end变量控制
print(1，2，3， seq="", end="\n")

# pprint（pretty print）可以输出格式化的数据
from pprint import pprint
pprint(data)
```

## random

```python
import random

# 随机浮点数
random.random()  # 0.5842210741472511
random.uniform(10,20)  # 14.802146907481868

# 随机整数
random.randint(1,10)  # 7

# 在等差数列中随机选一个数
random.randrange(0, 101, 2)  # 随机偶数
```

```python
_list = ['a', 'b', 'c', 'd']

# 随机打乱更新list元素的顺序
random.shuffle(_list)  # ['b', 'c', 'd', 'a']

# 从一个非空序列中随机选出1个str
random.choice(_list)  # "a"

# 从一个非空序列中随机出n个元素，不过以list格式返回
random.sample(_list, 2)  # ['b', 'd']

import string
"""
string.ascii_letters: 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'
string.digits: '0123456789'
"""
''.join(random.sample(string.ascii_letters + string.digits, 8))  # 'RTePaXy7'
```

## os

os模块封装了操作系统的目录和文件操作  
注意：有些在os模块中，有些在os.path模块中  
另外：**shutil** 模块是对os模块的一个补充

`import os`

### os.xxx

```python
os.name  # 操作系统类型（posix代表Linux/Unix/Mac OS X，nt代表Windows）
os.uname()  # 获取详细操作系统信息（Windows不支持）
```

```python
os.environ  # 环境变量
os.environ.get('PATH')  # 获取PATH变量的值，不存在则报错

os.getenv('X', default=None)  # 注意，获取的变量类型是字符串类型
```

```python
os.mkdir()  # 创建目录
os.rmdir()  # 删除目录

os.rename()  # 重命名文件
os.remove()  # 移除文件
```

### os.path.xxx

```python
os.path.dirname(__file__)  # 获取文件的目录
os.path.dirname(os.path.dirname(__file__))  # 获取上级目录
os.path.abspath(__file__)  # 获取文件的绝对路径/完整路径，带文件名

os.path.splitext('/path/to/file.txt')  # ('/path/to/file', '.txt')

base_path = os.path.abspath(os.path.dirname(os.path.dirname(__file__)))
```

```python
# 检测文件路径是否存在，不存在则新建
result_path = "/xxx"
if not os.path.exists(result_path):
    os.mkdir(result_path)
```

```python
import os

filename = "./tmp/test.log"

os.makedirs(os.path.dirname(filename), exist_ok=True)
with open(filename, "wb") as f:
    pickle.dump(cookies, f)
```

### os.system()

```python
os.system("bash command")  # 运行shell命令
```

### os.walk()

```python
import os

# 基础方法
for dirpath, dirnames, filenames in os.walk(path):
    print(dirpath, dirnames, filenames)
    """
    har ['haha'] ['11.har']
    har/haha [] ['22.har']
    """

# 获取路径下指定后缀的所有文件名
def get_files(path, file_type=None):
    _list = []
    for dirpath, dirnames, filenames in os.walk(path):
        for name in filenames:
            _list.append(os.path.join(dirpath, name))

    if file_type:
        file_list = []
        for i in _list:
            if os.path.splitext(i)[1] == file_type:
                file_list.append(i)
        return file_list

    return _list

get_files("har")  # ['har/v11.har', 'har/1.txt', 'har/haha/22.har']
get_files("har", ".har")  # ['har/v11.har', 'har/haha/22.har']
```

## sys

```python
import sys

# 查看默认编码
sys.getdefaultencoding()  # Py3默认utf-8，Py2默认ascii

# 获取引入模块时会搜索哪些路径
sys.path

# 获取命令行传入的参数列表
sys.argv
"""
比如执行命令：python demo.py arg1 arg2 arg3 ...
sys.argv获取到的参数list为：["demo.py", "arg1", "arg2", "arg3", ...]
"""
```

## webbrowser

快速启动浏览器打开指定网站

```python
import webbrowser

webbrowser.open("http://baidu.com")
```

## subprocess

subprocess 是 Python 中执行操作系统级别的命令的模块

比如用 Python 调用 JS 并传递变量

```python
import subprocess
import json

def deposit(to_address):
    # 准备数据并序列化为 JSON
    data = {"receiverAddress": to_address}
    data_json = json.dumps(data)

    # 调用 Node.js 脚本并传递 JSON 数据
    # process = subprocess.run(["node", "transfer.js", data_json], capture_output=True, text=True)
    # print(process.stdout)

    # 异步调用
    process = subprocess.Popen(["node", "transfer.js", data_json], stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)
    stdout, stderr = process.communicate()
    return stdout if stdout else stderr
```

JS 中获取变量

```js
const input = process.argv[2];
const parsedInput = JSON.parse(input);
const receiverAddress = parsedInput.receiverAddress;
```

## 时间模块

> 参考文章：<https://iswbm.com/203.html>

### 时间戳 timestamp

时间戳本事没有时区的概念，同一时间戳可以被转换为不同时区的格式化或者结构化时间

```python
import time

# 获取当前时间戳
stp = time.time()
"""
<class 'float'>
1609670371.4864702
"""
stp_int = int(stp)  # 不带毫秒
```

### 获取时区 time_zone

UTC(Coordinated Universal Time)时区
CST(China Standard Time)时区，UTC+8

```python
from datetime import datetime, timezone, timedelta

utc_tz = timezone.utc
cst_tz = timezone(timedelta(hours=8))
```

```python
import pyzt

utc_tz = pytz.timezone('UTC')  # <UTC>
cst_tz = pytz.timezone("Asia/Shanghai")  # <DstTzInfo 'Asia/Shanghai' LMT+8:06:00 STD>
```

### 时间戳转CST

```python
from datetime import datetime

fmt = "%Y-%m-%d %H:%M:%S"

cst_struct = datetime.fromtimestamp(stp_int, cst_tz)  # 先转为结构化时间
cst = cst_struct.strftime(fmt)
```

```python
import pyzt

fmt = "%Y-%m-%d %H:%M:%S"

cst_struct = datetime.fromtimestamp(stp_int, cst_tz)  # 先转为结构化时间
cst = cst_struct.strftime(fmt)
```

### CST转时间戳

```python
import time

fmt = "%Y-%m-%d %H:%M:%S"

cst_struct = time.strptime(cst, fmt)  # 先转为结构化时间
stp = time.mktime(cst_struct)
stp_int = int(stp)
```

### 其它（待整理）

```python
# 时间戳转本地时区结构化时间
x = time.localtime()
time.mktime(x)  # 逆操作

# 时间戳转UTC时区结构化时间
x = time.gmtime()
calendar.timegm(x)  # 逆操作
"""
<class 'time.struct_time'>
time.struct_time(tm_year=2021, tm_mon=1, tm_mday=3, tm_hour=19, tm_min=17, tm_sec=8, tm_wday=6, tm_yday=3, tm_isdst=0)

x[0]  # 2021
x.tm_wday  # 6 表示周日
x.tm_yday  # 一年中的第几天
x.tm_isdst  # 是否夏令时
"""

# 结构化转ctime格式
time.asctime(x)
# 时间戳转ctime格式
x = time.ctime()  # <class 'str'> 'Sun Jan  3 20:25:19 2021'
# 结构化时间转格式化时间
x = time.strftime("%Y-%m-%d %H:%M:%S")  # <class 'str'> '2021-01-03 19:18:55'

x.strptime(strftime)  # strftime格式化转结构化时间
```

- datetime

```python
# 服务器所在时区的当前时间
datetime.datetime.now()  # <class 'datetime.datetime'>

# 标准UTC当前时间
datetime.datetime.utcnow()  # <class 'datetime.datetime'>
```
