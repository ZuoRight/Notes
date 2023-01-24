---
comments: true
hide:
  - feedback
---

# 安装与简介

Python，由荷兰人[吉多·范罗苏姆 (Guido van Rossum)](https://gvanrossum.github.io)所创建。于1991年发行了第一个正式版本，比Java还要早5年，在编程语言排行榜中稳居前三。

## 安装

> 参考：<https://docs.python.org/zh-cn/3.9/using/index.html>

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

### Ubuntu 16.10+

```bash
sudo apt-get update
sudo apt-get install python3.8
```

### CentOS 7+

```bash
# 官方源没有，得用epel源
sudo yum install epel-release
# 不一定包含最新版Python
yum search python
# 安装3.7，不包含pip
sudo yum install python37
# 安装setuptools，与Python版本保持一致
yum install python37-setuptools
# 安装pip
easy_install pip
```

### Docker

> 参考：<https://hub.docker.com/_/python>

```bash
sudo docker run -dit --name demo python:3.9 "/bin/bash"
sudo docker exec -it demo "/bin/bash"
```

```bash
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

## 注释

- `#` 注释符则基本上是被 Shell 和其它脚本语言所用，Python属于脚本语言，用的也是这个
- `//` 注释符基本上被 C 语言家族所用

## 终止符

在Python中，使用换行符来终止语句，可使用反斜杠满足一句多行（实际就是把换行符转义了），建议使用括号来换行。

> 常见的编程语言（C、Java、JS等）基本都使用了分号，既可以作为分割符（满足一行多句），又可以作为终止符（满足一句多行）
>
> 参考：[编程语言中分号的简明历史](https://mp.weixin.qq.com/s/VLJZjMp1OuMDwIiL4NH1_g)

## 代码块

Python中使用了冒号和缩进来表示代码块，而不是花括号，更加简洁

> 从最初的`BEGIN...END`，演变为`$...$`，然后到C语言简化为`{...}`，很多类C语言基本都沿用了花括号。
>
> 参考：[编程语言中花括号的简明历史](https://mp.weixin.qq.com/s/8-DgLMBfWSnR0j8Q83UzeQ)

## 动态语言

Python是一门动态语言，动态语言在运行时才会检查数据类型，所以无需提前声明数据类型，同一变量可以反复赋值不同类型的数据。

- Python

``` python
x = 1  # 不需要关键字定义
x = None  # 初始值可以赋值为：None

x = y = z = 1  # 同时给多个变量赋相同值
x,y,z = 1,2,3  # 同时给多个变量赋不同值，要一一对应，否则会报错，比如：x,y,z=1 或 x,y=1,2,3 都是不对的

x,y = y,x  # 值交换
```

- JavaScript

``` javascript
// 需要关键字定义
// 未赋值时初始值为：undefined
var x = 1;
```

静态语言在编译时检查数据类型，所以需要提前声明，同一变量只能反复赋值同类型的数据。

- Java

``` java
// Java
// 未赋值时初始值为：0
int x = 1;
```

## Python之禅

- 简单：类似自然语言
- 明确：做任何事情应有且最好只有一个方法，少即是多。
- 优雅：准确的来说应该是实用

```bash
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

<https://docs.python.org/zh-cn/3.9/reference/index.html>