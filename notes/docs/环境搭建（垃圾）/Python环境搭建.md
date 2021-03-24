# 环境搭建

Python，于1991年发行了第一个正式版本，比Java还要早5年，目前在编程语言排行榜中已经稳居前三。由荷兰人[吉多·范罗苏姆 (Guido van Rossum)](https://gvanrossum.github.io)所创建。

## 下载安装

### 官方版

> [官方下载地址](https://www.python.org/downloads/)

- PC系统

直接去官网下载安装最新版即可

- Mac系统

自带了V2.7版本，路径：`/usr/bin/python`，建议不要随便卸载。

下载最新版本不推荐官网下载，因为卸载起来比较麻烦。

建议使用`brew install python`安装，安装路径：`/usr/local/cellar/python`），升级与卸载都很方便。

- Ubuntu16.10+

```bash
sudo apt-get update
sudo apt-get install python3.7
```

- CentOS7+

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

### 发行版

Python常被用于数据的处理，需要基于大量的第三方库，一个个手动安装这些库很麻烦，可以下载已经集成了这些库的发行版Python，比如Anaconda。

> [Anaconda下载地址](https://www.anaconda.com/download/)

## 包管理器

### pip

```shell
# 查看所有命令参数
pip

# 查看已安装的包
pip list
# 查看过期的包
pip list -o

# 查看某个库的具体信息(版本号、依赖、位置等)
pip show xxx
# 查询更加具体的信息
pip show -f xxx
```

```shell
# 下载安装，默认安装最新版本
pip install xxx
# 指定版本
pip install xxx==version
# 指定镜像源
pip install xxx -i 镜像源

# 更新包，也可以更新pip自己
pip install -U xxx

# 卸载包
pip uninstall xxx
# 卸载pip本身
pip -m pip uninstall pip
```

```shell
# 查看已安装的包
pip freeze
# 将环境中所有已安装的库及其版本导出到指定文件
pip freeze > requirements.txt
# 安装指定文件中列出的所有库
pip install -r requirements.txt
# 卸载指定文件中列出的所有库
pip uninstall -r requirements.txt
```

## 虚拟环境

### pyvenv

v3.5版本后已移除，无需了解

### venv

Python v3.3+ 自带，推荐

- 创建：`python -m venv env`

会在当前目录创建一个名为env（可自定义）的文件，删除它即删除了虚拟环境

激活虚拟环境：

- Mac/Linux: `source env/bin/activate`
- Windows: `env\Scripts\activate`

激活后命令提示符前会显示：(env)

- 退出：`deactivate.bat`

### virtualenv

需要额外安装：`pip install virtualenv`

除非还使用Python2.x版本，否则使用自带的venv即可

- 创建：`virtualenv xxx`
- 删除：`rm -rf xxx`（手动删除 *xxx* 文件夹亦可）

- 进入：`activate.bat`（需要先切换到 *xxx/scripts* 路径下）
- 退出：`deactivate.bat`

---

virtualenv不便于对虚拟环境集中管理，可以再安装一个虚拟环境管理器virtualenvwrapper

- 安装：`pip install virtualenvwrapper-win`

- 创建：`mkvirtualenv xxx`
- 删除：`rmvirtualenv xxx`

- 进入：`workon xxx`
- 退出：`deactivate.bat`

- 查看：`workon`

## 包管理器+虚拟环境

### conda

虽然conda来自Python社区且由Python语言开发，但它不止可以管理Python语言及Python的包，还可以管理其他语言的包（Java，R，Ruby，JS，Lua，Scala，C/C++，Fortran）

- 创建：`conda create -n xxx python=3`
- 删除：`conda remove -n xxx --all`

- 进入：`activate xxx`
- 退出：`deactivate xxx`

- 查看：`conda info --envs`

### pipenv

由requests库的作者Kenneth Reitz基于pip和virtualenv所编写

参考：<https://www.liujiangblog.com/blog/18/>

安装：

- Windows：`pip install pipenv`
- Mac：`brew install pipenv`

命令格式：`pipenv [options] command [args]`

#### 初始化项目

如果是新项目，可以先用`pipenv --python 3`初始化(可指定具体版本)一个虚拟环境，查看当前环境所在路径：`pipenv --venv`，一般默认路径为：

- Windows：`~\.virtualenvs\`
- Mac：`~/.local/share/virtualenvs/`

> 删除虚拟环境：`pipenv --rm`

项目路径下会生成一个TOML格式的Pipfile文件，内容如下：

```TOML
[[source]]
url = "https://pypi.org/simple"
verify_ssl = true
name = "pypi"

[packages]

[dev-packages]

[requires]
python_version = "3.7"
```

其中url字段为官方下载源，比较慢，建议替换为国内镜像源，比如：`https://mirrors.aliyun.com/pypi/simple/`。

#### 安装项目依赖

`packages`和`dev-packages`节点下列出了生产和开发环境所需的依赖，可以使用`pipenv install [--dev]`一键安装

> tips：如果没有初始化项目(或者说没有Pipfile文件)，直接执行`pipenv install [--two/three]会自动初始化并生成Pipfile。

还可以从requirements.txt中安装：`pipenv install -r requirements.txt`

当然也可以单个安装

- 安装：`pipenv install xxx`
- 卸载：`pipenv uninstall xxx`
- 查看当前环境路径：`pipenv venv`
- 查看依赖关系及信息：`pipenv graph`
- 更新所有依赖：`pipenv update`

安装完依赖后会自动生成一个JSON格式的`Pipfile.lock`文件，保存着所有依赖的版本和hash信息（默认使用sha256算法给每一个包进行hash，可以保证在不安全的网络环境下也能下载到正确的包），每次更新或卸载依赖包都会更新此文件，即lock，类似于当前环境的一个快照，不要手动修改其内容。也可以手动lock：`pipenv lock`。

- 生成依赖列表（为了迁移回venv）：`pipenv lock -r`

#### 运行项目

`pipenv run python xxx.py`

如果python命令比较长，可以在Pipfile中添加`[scripts]`节点，然后创建一个别名，比如：

`alias = "python3 manage.py runserver 0.0.0.0:8000"`

然后执行`pipenv run alias`即可

当然还可以`pipenv shell`进入到虚拟环境中执行，退出：`exit`。

## 编辑器

### [PyCharm](https://www.jetbrains.com/pycharm/)

专业的Python IDE，由位于捷克的[JetBrains公司](https://www.jetbrains.com/)所生产。

具体使用方法可参考：[PyCharm 中文指南](http://pycharm.iswbm.com/zh_CN/latest/)

配置pipenv？

### VSCode

Python 开发所相关的一些插件：

- 1
- 2

## 解释器

参考文章：

<https://www.cnblogs.com/mq0036/p/5014236.html>

<http://www.omegaxyz.com/2018/02/21/cpython/>

### CPython

CPython把Python源码编译成字节码（.pyc），然后再解释这些字节码，执行之。

### PyPy

由Python的一个子集RPython所编写，所以并不是自举。

## Python哲学

- 简单：类似自然语言
- 明确：做任何事情应有且最好只有一个方法，少即是多。
- 优雅：准确的来说应该是实用

```Python
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
Special cases aren't special enough to break the rules.
Although practicality beats purity.
# 即便假借特例的实用性之名，也不可违背这些规则
Errors should never pass silently.
Unless explicitly silenced.
# 不要包容所有错误，除非你确定需要这样做
In the face of ambiguity, refuse the temptation to guess.
# 当存在多种可能，不要尝试去猜测
There should be one-- and preferably only one --obvious way to do it.
# 而是尽量找一种，最好是唯一一种明显的解决方案
Although that way may not be obvious at first unless you're Dutch.
# 虽然这并不容易，因为你不是荷兰人（即 Guido）
Now is better than never.
Although never is often better than *right* now.
# 做也许好过不做，但不假思索就动手还不如不做
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
# 如果你无法向人描述你的方案，那肯定不是一个好方案，反之亦然
Namespaces are one honking great idea -- let's do more of those!
# 命名空间是一种绝妙的理念，我们应当多加利用
```

## 规范

### 终止语句不使用分号

常见的编程语言（C、Java、JS等）基本都使用了分号，既可以作为分割符（满足一行多句），又可以作为终止符（满足一句多行）。

在Python中，使用换行符来终止语句，可使用反斜杠满足一句多行（实际就是把换行符转义了），建议使用括号来换行。

参考：[编程语言中分号的简明历史](https://mp.weixin.qq.com/s/VLJZjMp1OuMDwIiL4NH1_g)

### 代码块不使用花括号

从最初的`BEGIN...END`，演变为`$...$`，然后到C语言简化为`{...}`，很多类C语言基本都沿用了花括号，而Python中使用了冒号和缩进来表示代码块，更加简洁。

参考：[编程语言中花括号的简明历史](https://mp.weixin.qq.com/s/8-DgLMBfWSnR0j8Q83UzeQ)

### 注释语句

- `//` 注释符基本上被 C 语言家族所用

- `#` 注释符则基本上是被 Shell 和其它脚本语言所用，Python属于脚本语言，用的也是这个

## 隐私数据脱敏

不同的环境需要不同的配置信息，但这些配置信息一般不希望暴露给别人，尤其是开源项目代码时

所以可以考虑将配置存储于环境变量中，环境变量可以非常方便地在不同的部署间做修改，而不需要修改代码

配置环境变量有三种方式：

1. 手动配置，比较麻烦
2. supervisor进程管理工具，比较混乱
3. 自动导入环境变量的工具：python-dotenv，推荐

安装：`pip install python-dotenv`

```python
# 将环境变量写入项目跟路径下的.env文件，并加入.gitignore规则中忽略其版本管理
# 加载环境变量，flask、django等框架默认会加载，所以不需要这两行代码
from dotenv import load_dotenv
load_dotenv()

# 获取配置信息
import os

value = os.getenv("KEY")
```

在flask项目开发中，可以将隐私数据写到.env中，而一些不隐私的变量，写到.flaskenv文件中

```shell
FLASK_APP=app.py  # 执行flask run默认运行app.py文件，不存在则会报错
FLASK_ENV=production  # 默认为生产环境，可改为开发环境：development，开启调试模式（启动项目时提示Debugger is active!）
```

Flask 提供了一个统一的接口来写入和获取这些配置变量：Flask.config字典，

```python
app.config["KEY"] = value
```

配置变量名称必须大写，写入配置的语句一般会放到扩展类实例化语句之前

配置文档：<https://flask.palletsprojects.com/en/1.0.x/config/>
