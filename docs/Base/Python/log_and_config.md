# 日志与配置

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

## python-dotenv

项目开发（尤其是代码开源）时需要考虑隐私数据脱敏，比如私钥，密码等

通常会存储在环境变量中，配置环境变量有三种方式：

1. 手动配置，比较麻烦
2. 自动导入环境变量的工具：python-dotenv，推荐
3. supervisor进程管理工具，比较混乱

安装：`pip install python-dotenv`

> 文档：<https://pypi.org/project/python-dotenv>

创建一个 `.env` 文件，通常存放在根路径，记得把 `.env` 文件添加到 `.gitignore`

```shell
PRIVATE_KEY="xxxx"

# Flask Config
FLASK_APP=app.py  # 执行flask run默认运行app.py文件，不存在则会报错
FLASK_ENV=production  # 默认为生产环境，可改为开发环境：development，开启调试模式（启动项目时提示Debugger is active!）
```

- 获取环境变量，方式1：load_dotenv

```python
# flask、django等框架默认会加载，所以不需要以下两行代码引入
from dotenv import load_dotenv
load_dotenv(override=True)  # 默认会覆盖已存在的环境变量，如果不想覆盖override设置为False
"""
load_dotenv('/path/to/your/.env')  # 如果不在根路径，加载时需要指定路径
load_dotenv(find_dotenv())  # 可以通过引入find_dotenv()方法自动查找
"""

import os
value = os.getenv("KEY", "default-value")
"""
返回的值都是str类型的，如果要dict可以使用json.loads(value)
"""
```

- 获取环境变量，方式2：dotenv_values

不会将变量加载到环境变量中，而是返回一个OrderedDict对象

```python
from dotenv import dotenv_values
config = dotenv_values(".env")  # 或者指定任何你想要读取的 .env 文件的路径

print(config["KEY"])
```
