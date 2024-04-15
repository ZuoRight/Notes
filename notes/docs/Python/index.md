---
comments: true
hide:
  - feedback
---

# 引言

Python，由荷兰人[吉多·范罗苏姆 (Guido van Rossum)](https://gvanrossum.github.io)所创建。于1991年发行了第一个正式版本，比Java还要早5年，在编程语言排行榜中稳居前三。

## 安装

> 参考：<https://docs.python.org/zh-cn/3/using/index.html>

### Windows

直接去官网下载安装最新稳定版即可

### Mac

- 系统预装

MacOS10.8~12.3（M1之前）预装了Python2.7版本，被一些应用所依赖，所以不能卸载

安装路径：`/System/Library/Frameworks/Python.framework`和`/usr/bin/python`

- 官网下载

安装路径：`/Library/Frameworks/Python.framework/Versions/3.x`

- homebrew安装

`brew install python`

注意：使用homebrew安装一些依赖Python的工具时，会自动升级或安装相应版本的Python，比如mitmproxy最新版，会自动安装Python3.10

系统会存在多个版本的Python，执行命令时可以带上版本来使用：`Python3.9`、`Python3.10`

### Ubuntu

- Ubuntu 18.04.6 LTS (Bionic Beaver) 内置Py3.6
- Ubuntu 20.04.5 LTS (Focal Fossa) 内置Py3.8
- Ubuntu 22.04.1 LTS (Jammy Jellyfish) 内置Py3.10

```shell
sudo apt-get update
sudo apt-get install python3.9
```

### CentOS 7+

```shell
# 官方源没有，得用epel源
sudo yum install epel-release
# 不一定包含最新版Python
yum search python
# 安装3.9，不包含pip
sudo yum install python39
# 安装setuptools，与Python版本保持一致
yum install python39-setuptools
# 安装pip
easy_install pip
```

### Docker

> 参考：<https://hub.docker.com/_/python>

```shell
docker pull python:3.9  # 默认Ubuntu环境
docker run -d -it --name demo python:3.9 "/bin/bash"
```

```shell
FROM python:3.9

# ENV PYTHONUNBUFFERED=1
'''
不缓冲stdin、stdout和stderr，默认是缓冲的
相当于设置 python 命令行的 -u 选项
'''

WORKDIR /usr/src/app
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

# CMD ["python", "manage.py", "runserver"]
```

## 普通命令行

> <https://docs.python.org/3.9/using/cmdline.html#command-line-and-environment>

## 交互式命令行

- Python Shell
- [IPython](https://ipython.org/)

Python Shell 是自带命令行工具，常用于验证一些代码片段，而 IPython 是基于 Python Shell 的第三方开源交互命令行，更强大，它其实是Jupyter的内核。

`pip install ipython`

## IDE

- IDLE

IDLE是自带的简易集成开发和学习环境

快捷键：`Alt+P` 上一句代码，`Alt+N` 下一句代码

- PyCharm

[PyCharm](https://www.jetbrains.com/pycharm/)是捷克公司JetBrains为Python语言所生产的商业IDE，社区版免费

具体使用方法可参考：[PyCharm 中文指南](http://pycharm.iswbm.com/zh_CN/latest/)

PyCharm默认会在创建项目时新建一个虚拟环境，默认名称venv

- VSCode

VSCode并不算IDE，只是一个编辑器，个人比较喜欢用这个开发Python，而不是更智能的PyCharm，因为我觉得它过于傻瓜化，用习惯后会影响我的开发能力。

## Python之禅

- 简单：类似自然语言
- 明确：做任何事情应有且最好只有一个方法，少即是多。
- 优雅：准确的来说应该是实用

```shell
>>> import this

The Zen of Python, by Tim Peters
# Python 之禅，作者：Tim Peters

Beautiful is better than ugly.
# 优雅胜于丑陋
Explicit is better than implicit.
# 明确胜于晦涩
Simple is better than complex.
# 简洁胜于复杂
Complex is better than complicated.
# 复杂胜于凌乱
Flat is better than nested.
# 扁平胜于嵌套
Sparse is better than dense.
# 间隔胜于紧凑
Readability counts.
# 可读性很重要
Special cases are not special enough to break the rules.
Although practicality beats purity.
# 即便假借特例的实用性之名，也不可违背这些规则
Errors should never pass silently.
Unless explicitly silenced.
# 不要包容所有错误，除非你确定需要这样做
In the face of ambiguity, refuse the temptation to guess.
# 当存在多种可能，不要尝试去猜测
There should be one-- and preferably only one --obvious way to do it.
# 而是尽量找一种，最好是唯一一种明显的解决方案
Although that way may not be obvious at first unless you are Dutch.
# 虽然这并不容易，因为你不是荷兰人（即 Guido）
Now is better than never.
Although never is often better than *right* now.
# 做也许好过不做，但不假思索就动手还不如不做
If the implementation is hard to explain, it^s a bad idea.
If the implementation is easy to explain, it may be a good idea.
# 如果你无法向人描述你的方案，那肯定不是一个好方案，反之亦然
Namespaces are one honking great idea -- lets do more of those!
# 命名空间是一种绝妙的理念，我们应当多加利用
```

## 参考手册

- 官方文档：<https://docs.python.org/zh-cn/3.9/reference/index.html>
- PEP(Python Enhancement Proposals)：<https://peps.python.org/>
