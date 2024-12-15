# 模块和包

Module < Package < Library < Framework

- 模块，即一个 `.py` 或 `.so` 等文件，一堆函数、类、变量的集合，其它语言中一般只能包含一个类，而 Python 可以包含多个
- 包：模块的集合
- 库：指一定功能的代码集合，可由多个包和模块组成
- 框架：可以集成各种库

Python 的模块不需要 `main` 函数，参考：[Python为什么没有main函数？](https://mp.weixin.qq.com/s/1ehySR5NH2v1U8WIlXflEQ)

```python
#!/usr/bin/env python3  # 告诉Linux/Unix系统执行程序时运行哪个解释器
# coding:utf-8  # 或-*- coding: utf-8 -*-，表示文件使用标准UTF-8编码，python3已经不需要加这个了

'''任何模块代码的第一个字符串都被视为模块的文档注释'''

__author__ = '7c'  # 标注作者


"""
引入书写优先级：内置模块 > 第三方模块 > 自定义模块
"""
import sys
import os
# sys.path.append("..")
sys.path.append(os.path.dirname(os.path.dirname(os.path.realpath(__file__))))
from a import am


# 查看模块可以直接调用的方法
print(xxx.__all__)
print(dir(xxx))

def b1():
    am.a1()
    print("我是b1")
```

## 导入方式

假设项目结构如下

```css
demo/
│
├── a.py
└── b.py
```

```python
# 直接导入
# 需要在搜索路径中
from a import Xxx

# 相对导入
# 只能在包内部使用，不能用于直接执行脚本
from .a import Xxx

# 绝对导入
# 要求demo必须是一个包，且在搜索路径中
from demo.a import Xxx
```

```python
import xx.xx

import importlib

# 模块名如果是数字开头，可以动态导入
two = importlib.import_module("xx.0002_remove_content_type_name")

# 导入后可重命名，可用于简写模块名，或者避免命名冲突
from xx.xx import xx [as rename]

# 导入全部，容易发生命名冲突，慎用
# 可以在__init__.py模块中定义 __all__ = ["module_name"] 变量，控制*的范围
from xx.xx import *
```

## 默认搜索顺序

`sys.path`

1. 自定义模块：当前执行脚本所在目录
2. 内置模块：Python的安装目录
3. 环境变量：PYHONPATH路径
4. 第三方模块：Python安装目录里的site-packages目录

## `__init__.py`

在 Python 3.3 之前，一个目录必须包含 `__init__.py` 文件才能被 Python 视为一个包。这个文件可以是空的，但它的存在告诉 Python 解释器把这个目录当作一个包来处理。

从 Python 3.3 开始，引入了所谓的隐式包（Implicit Namespace Packages）。这意味着，即使没有 `__init__.py` 文件，目录也可以被视为包。这允许了更加灵活的包结构，特别是在处理大型项目或跨多个目录分布的代码时。

不过 `__init__.py` 仍然有其用途

- 初始化代码：比如包级别的变量初始化、包的子模块导入等
- 控制可导入的内容：通过在 `__init__.py` 中定义 `__all__ = []` 可以精确控制包导入时应该暴露哪些模块和符号
- 向后兼容性：兼容老版本 Python 的项目

## `__file__`

当前文件的路径，通常用于确定当前脚本的位置或构建相对于脚本的文件路径

```python
import os

script_directory = os.path.dirname(os.path.abspath(__file__))
```

## `__name__`

当前文件名，在脚本或模块被执行时设置

- 如果文件作为脚本直接执行，则被设置为 `__main__`
- 如果作为模块被导入，则设置为 `文件名`

通常用来在一个文件中区分代码是作为脚本直接运行还是作为模块被导入

```python
if __name__ == "__main__":
    pass
```
