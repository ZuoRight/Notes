# 调试

## Pdb

在想要调试的地方前面加一个 `breakpoint()`，然后正常运行程序，执行到断点处便可以进入到 Pdb 调试模式

Pdb 命令行中可以运行任何 Python 命令，检视程序的同时，还可以改变程序状态，比如改变变量值

```python
def demo(data):
    return data * data

def f(x):
    breakpoint()  # 断点
    lst = []
    for i in range(x):
        val = demo(i)
        lst.append(val)
    return lst

f(3)
```

> 参考：<https://www.bilibili.com/video/BV1La4y1T7Y5>

```python
p x  # print(x) 打印，任何Python表达式都可以

l  # 查看当前行前后11行的代码，继续l会往下翻
l .  # 回到当前行
ll  # longlist 可显示当前函数的全部代码

w  # where 查看调用栈，左边箭头标识当前所在帧/frame
u  # up 进入上一帧
d  # down 进入下一帧

n  # next 执行下一行代码，如果有函数调用，调用完进入下一行
s  # step 执行下一行代码，如果有函数调用，进入调用函数
r  # 运行完当前函数，可以用 retval 拿到返回值
c  # continue 继续运行到下一个断点处

until  # 运行直到函数比我当前函数大为止，比如将循环运行完跳出循环
until(7)  # 直接运行到第7行

q  # 退出调试
```

也可以不在代码中加断点，直接执行：`python -m pdb demo.py`，会在第一行代码停下来

```python
b  # break 列出所有断点
b 5  # 在第5行设置breakpoint
b demo  # 在demo函数中设置断点，运行到此处直接进入断点函数中

clear  # 清除所有断点
clear 1  # 清除指定断点
```

## logging

> 参考手册：<https://docs.python.org/zh-cn/3.11/howto/logging.html#logging-basic-tutorial>

基础

```python
import logging

logging.basicConfig(
    filename='example.log', 
    encoding='utf-8', 
    format='%(asctime)s %(message)s', datefmt='%m/%d/%Y %I:%M:%S %p',  # format='%(levelname)s:%(message)s'
    filemode='w',  # 翻盖
    level=logging.DEBUG
)

logging.info("xxx")
```

日志库采用模块化方法，并提供几类组件：记录器、处理器、过滤器和格式器

```python
import logging

# 创建一个日志记录器
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)  # 设置日志记录级别为DEBUG

# 创建一个文件处理器，将日志消息输出到文件
file_handler = logging.FileHandler('my_log.log')
file_handler.setLevel(logging.DEBUG)  # 设置处理器的日志记录级别为DEBUG

# 创建一个控制台处理器，将日志消息输出到控制台
console_handler = logging.StreamHandler()
console_handler.setLevel(logging.INFO)  # 设置处理器的日志记录级别为INFO

# 创建一个格式化器，定义日志消息的格式
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
file_handler.setFormatter(formatter)
console_handler.setFormatter(formatter)

# 将处理器添加到日志记录器
logger.addHandler(file_handler)
logger.addHandler(console_handler)

# 发出不同级别的日志消息
logger.debug('这是一条调试消息')
logger.info('这是一条信息消息')
logger.warning('这是一条警告消息')
logger.error('这是一条错误消息')
logger.critical('这是一条严重消息')
```

配置文件

```python
import logging
import logging.config

logging.config.fileConfig('logging.conf')

# create logger
logger = logging.getLogger('simpleExample')
```

```conf
[loggers]
keys=root,simpleExample

[handlers]
keys=consoleHandler

[formatters]
keys=simpleFormatter

[logger_root]
level=DEBUG
handlers=consoleHandler

[logger_simpleExample]
level=DEBUG
handlers=consoleHandler
qualname=simpleExample
propagate=0

[handler_consoleHandler]
class=StreamHandler
level=DEBUG
formatter=simpleFormatter
args=(sys.stdout,)

[formatter_simpleFormatter]
format=%(asctime)s - %(name)s - %(levelname)s - %(message)s
```
