# 工具

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

## 代码分析工具 Linter

![从头检测 CPython 代码库的结果对比](https://mmbiz.qpic.cn/mmbiz_png/LLRiaS9YfFTMaRu30ej8Lez7W6H5GCTIlNs4FkQugAkicFSiaX9IvVxdcN94F7Xh82kwLPshoiaavbyicibgrI4JkHdg/640?wx_fmt=png&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)

与其它工具都是由 Python 编写不同，Ruff 是基于 Rust 的，支持 VSCode 插件

```shell
pip install ruff

ruff check .                        # 分析当前及子目录内的所有文件
ruff check path/to/code/            # 分析指定目录及子目录内的所有文件
ruff check path/to/code/*.py        # 分析指定目录内的所有py文件
ruff check path/to/code/to/file.py  # 分析 file.py
```

## 类型检查工具

- Mypy
- Pytype
- Pyright：VSCode的插件Pylance便基于此
- Pyre
