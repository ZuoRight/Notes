# Library

模块，即一个`.py`文件，一堆函数、类、变量的集合

> 类似其他语言中的库或者组件，其它语言中一般只能包含一个类，而Python可以包含多个
>
> 而且Python的模块不需要`main`函数，参考：[Python为什么没有main函数？](https://mp.weixin.qq.com/s/1ehySR5NH2v1U8WIlXflEQ)

- 包(Package)

模块的集合，Py3.3之前的版本必须带`__ini__.py`, 现在可有可无，可以做一些包的初始化工作

- 库

指一定功能的代码集合，通常认为他是一个完整的项目打包，可由多个包和模块组成

> 第三方库: pypi.python.org
>
> 还可下载.whl文件，使用pip install 安装.whl文件

## 导入

```python
import xx.xx  # 一般用于导入内置模块
from xx.xx import xx  # 一般用于导入第三方及自定义模块
from xx.xx import xx as rename  # 导入后重命名，可用于简写模块名，或者避免命名冲突
# 导入全部，容易发生命名冲突，慎用，可以在__init__.py模块中创建__all__ = ["module_name"]变量，定义*的范围
from xx.xx import *
# 模块名如果是数字开头，可以动态导入
import importlib
two = importlib.import_module("xx.0002_remove_content_type_name")
```

默认搜索顺序：`sys.path`

1. 自定义模块：当前执行脚本所在目录
2. 内置模块：Python的安装目录
3. 系统环境变量PYHONPATH路径
4. 第三方模块：Python安装目录里的site-packages目录

## 示例

```python
#!/usr/bin/env python3  # 告诉Linux/Unix系统执行程序时运行哪个解释器
# coding:utf-8  # 或-*- coding: utf-8 -*-，表示文件使用标准UTF-8编码，python3已经不需要加这个了

'''任何模块代码的第一个字符串都被视为模块的文档注释'''

__author__ = '7c'  # 标注作者


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

"""
全局变量__name__为当前模块名
当模块被直接执行时，模块名为："__main__"
当模块被导入时，模块名为："文件名"
"""
if __name__ == "__main__":
    b1()
```
