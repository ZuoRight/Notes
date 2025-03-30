---
comments: true
hide:
  - feedback
---

# 开发环境

- pip + venv
- pip + virtualenv + virtualenvwrapper
- pipenv

版本管理

- pyenv
- conda
- uv 推荐，Rust 编写的

## pip

> 文档：<https://pip.pypa.io/en/stable/cli/>

老版本的 Python 中只有 easy_install，easy_install 只能 install 不能 uninstall，也不能 freeze 出 `requirements.txt`

pip 是 easy_install（setuptools）的改进版，提供更好的提示信息，删除 package 等功能

![20230205022458](https://image.zuoright.com/20230205022458.png)

pip 可以下载安装

- Python Package Index: <https://pypi.python.org>
- Github
- distribution file: `.sdist`, `.whl`
- requirements.txt

如果觉得官方下载源比较慢，可以设置镜像

- `~/.pip/pip.conf`

```conf
[global]
index-url = https://mirrors.aliyun.com/pypi/simple/
trusted-host=mirrors.aliyun.com
```

- `~/.pydistutils.cfg`

```conf
[easy_install]
index-url = http://mirrors.tencentyun.com/pypi/simple    
```

```shell
pip  # 查看所有命令参数

pip list  # 查看已安装的包
pip list -o  # 查看过期的包

pip show xxx  # 查看某个库的具体信息(版本号、依赖、位置等)
pip show -f xxx  # 查询更加具体的信息
```

```shell
pip search xxx  # 搜索包，PyPI no longer supports pip search (or XML-RPC search). Please use https://pypi.org/search (via a browser) instead.

pip download xxx  # 下载包

pip install xxx  # 安装，默认安装最新版本
pip install xxx==version  # 指定版本
pip install [--user] xxx  # 安装到～/.local/bin
pip install --no-cache-dir xxx  # 不使用本地缓存安装
pip install xxx -i 镜像源 --trusted-host 镜像地址对应的host  # 指定镜像源

pip install --upgrade xxx  # 更新包，也可以更新pip自己

pip uninstall xxx  # 卸载包
```

- 查看安装包大小

```shell
pip list | tail -n +3 | awk '{print $1}' | xargs pip show | grep -E 'Location:|Name:' | cut -d ' ' -f 2 | paste -d ' ' - - | awk '{print $2 "/" tolower($1)}' | xargs du -sh 2> /dev/null | sort -hr
```

### requirements.txt

```shell
# 注释
requests==2.25.1  # 指定精确版本
flask>=1.1.2  # 大于等于
numpy<=1.19.5  # 小于等于

pandas~=1.2.0  # 兼容版本，安装 1.2.x 系列的最新版本

Django>=2.2,<3.0  # 范围

flask[dev]  # 安装额外依赖

# 指定 GitHub 或 Git 仓库中的包
git+https://github.com/username/repository.git@branch

# 指定本地路径的包
-e ./path/to/local/package
```

- 自动生成

```shell
# 查看已安装的包
pip freeze
# 将环境中所有已安装的库及其版本导出到指定文件
pip freeze > requirements.txt
```

第三方插件 pipreqs 是一个专门用于生成 requirements.txt 文件的工具，只会包含实际使用到的库

- 安装&卸载

```shell
# 安装指定文件中列出的所有库
pip install -r requirements.txt

# 卸载指定文件中列出的所有库
pip uninstall -r requirements.txt
```

### pipdeptree

> <https://github.com/naiquevin/pipdeptree>

```shell
pip install pipdeptree  # 单独安装

pipdeptree  # 可以按依赖关系以树形列出已安装的包
pipdeptree --reverse --packages xxa,xxb  # 列出依赖了xxa或xxb的包
pipdeptree --json-tree   # json嵌套格式返回
pipdeptree --graph-output png > dependencies.png  # 生成可视化的依赖图，需要安装graphviz，具体查看文档
```

### pip-autoremove

```shell
pip install pip-autoremove

<<"COMMENT"
Usage: pip-autoremove [OPTION]... [NAME]...

Options:
  --version     show program's version number and exit
  -h, --help    show this help message and exit
  -l, --list    list unused dependencies, but don't uninstall them.
  -L, --leaves  list leaves (packages which are not used by any others).
  -y, --yes     don't ask for confirmation of uninstall deletions.
COMMENT

# 比如卸载Flask，可以将依赖包一并卸载
pip-autoremove Flask -y
```

## venv

Python v3.6 开始，官方移除了`·pyenv`，推荐使用新的 `venv` 来创建虚拟环境

```shell
# 在当前目录创建一个名为env(可自定义)的虚拟环境，删除它即删除了虚拟环境
python3 -m venv venv [--without-pip]
"""
为了确保在安装 Python 或创建虚拟环境时不需要网络访问，ensurepip 模块将会把 pip 的完整副本及其依赖项安装到目标环境中
可以使用 --without-pip 选项不让 ensurepip 将 pip 引导到虚拟环境中

On Debian/Ubuntu systems, ensurepip is not available,
you need to install the python3-venv package using the following command.
  sudo apt-get update
  sudo apt install python3.x-venv
关于此事的一个讨论：https://bugs.launchpad.net/ubuntu/+source/python3.4/+bug/1290847
"""

# 激活虚拟环境，激活后命令提示符前会显示：(env)
source venv/bin/activate  # Mac/Linux
venv\Scripts\activate  # Windows

# 安装项目依赖
pip install -r requirements.txt

# 退出虚拟环境
deactivate
```

## virtualenv + virtualenvwrapper

`pip install virtualenv`

除非还使用 Python2.x 版本，否则使用自带的 venv 即可

```shell
virtualenv xxx  # 创建
rm -rf xxx  # 删除，或者手动删除对应文件夹

activate.bat  # 进入
deactivate.bat  # 退出
```

为了便于对虚拟环境集中管理，可搭配虚拟环境管理器 virtualenvwrapper 使用

`pip install virtualenvwrapper-win`

```shell
mkvirtualenv xxx  # 创建
rmvirtualenv xxx  # 删除
workon  # 查看

workon xxx  # 进入
deactivate.bat  # 退出
```

## pipenv

由 Requests 库的作者 Kenneth Reitz 基于 pip 和 virtualenv 编写的

> 参考：<https://www.liujiangblog.com/blog/18/>

- Windows：`pip install pipenv`
- Mac：`brew install pipenv`

命令格式：`pipenv [options] command [args]`

### 初始化项目

如果是新项目，可以先用 `pipenv --python 3` 初始化（可指定具体版本）一个虚拟环境，查看当前环境所在路径：`pipenv --venv`，一般默认路径为：

- Windows：`~\.virtualenvs\`
- Mac：`~/.local/share/virtualenvs/`

> 删除虚拟环境：`pipenv --rm`

项目路径下会生成一个 TOML 格式的 Pipfile 文件，内容如下：

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

官方下载源比较慢，可替换为国内镜像源，比如：`https://mirrors.aliyun.com/pypi/simple/`

### 安装项目依赖

`packages` 和 `dev-packages` 节点下列出了生产和开发环境所需的依赖，可以使用 `pipenv install [--dev]` 一键安装

> tips：如果没有初始化项目（或者说没有 Pipfile 文件），直接执行 `pipenv install [--two/three]` 会自动初始化并生成 Pipfile。

```shell
pipenv install -r requirements.txt  # 从requirements.txt中安装
pipenv install xxx  # 安装
pipenv uninstall xxx  # 卸载

pipenv venv  # 查看当前环境路径
pipenv graph  # 查看依赖关系及信息
pipenv update  # 更新所有依赖
```

安装完依赖后会自动生成一个 JSON 格式的 `Pipfile.lock` 文件，保存着所有依赖的版本和 hash 信息（默认使用 sha256 算法给每一个包进行 hash，可以保证在不安全的网络环境下也能下载到正确的包），每次更新或卸载依赖包都会更新此文件，即 lock，类似于当前环境的一个快照，不要手动修改其内容。也可以手动 lock：`pipenv lock`。

- 生成依赖列表（为了迁移回 venv）：`pipenv lock -r`

### 运行项目

`pipenv run python xxx.py`

如果 Python 命令比较长，可以在 Pipfile 中添加 `[scripts]` 节点，然后创建一个别名，比如：

`alias = "python3 manage.py runserver 0.0.0.0:8000"`

然后执行 `pipenv run alias` 即可

当然还可以 `pipenv shell` 进入到虚拟环境中执行，退出：`exit`

## pipx

经常发现一些框架推荐使用 `pipx insgall xxx` 的方式来安装，究其原因是这些框架依赖过多，pipx 可以将它们安装到自动创建的虚拟环境中。

个人认为使用 venv 创建虚拟环境，然后用 pip 安装足矣。

## uv

<https://docs.astral.sh/uv/>

```shell
# 安装
curl -LsSf https://astral.sh/uv/install.sh | sh
# 或
wget -qO- https://astral.sh/uv/install.sh | sh

# 添加环境变量到 ~/.zshrc
. "$HOME/.local/bin/env"
# 自动补全
echo 'eval "$(uv generate-shell-completion zsh)"' >> ~/.zshrc
echo 'eval "$(uvx --generate-shell-completion zsh)"' >> ~/.zshrc

# 升级
uv self update

# 卸载
uv cache clean
rm -r "$(uv python dir)"
rm -r "$(uv tool dir)"
```

```shell
# 查看帮助
uv

# 查看可安装的python版本
uv python list
# 安装python版本
uv python install  # 最新版本
uv python install 3.12  # 指定版本
uv python install 3.11 3.12  # 安装多个版本
uv python install --reinstall  # 重新安装
```

默认情况下，如果在系统上找不到 Python 版本，uv 将自动下载 Python 版本

通过 uv 安装的 Python 将不会被全局可用

```shell
uv venv
uv venv --python 3.11.6

source .venv/bin/activate  # 激活
deactivate  # 退出
```

```shell
# 初始化项目
uv init hello-world
'
.
├── .venv  虚拟环境
│   ├── bin
│   ├── lib
│   └── pyvenv.cfg
├── .python-version  此文件告诉 uv 在创建项目的虚拟环境时要使用哪个 Python 版本
├── README.md
├── main.py
├── pyproject.toml  使用此文件指定依赖项，以及有关工程的详细信息
└── uv.lock  跨平台的 lockfile，其中包含有关项目依赖项的确切信息，不应手动编辑
'
# 添加依赖到 pyproject.toml，将更新 lockfile 和项目环境
uv add requests
uv add 'requests==2.31.0'
# 删除依赖
uv remove requests
# 手动同步
uv sync
```

```shell
# 运行脚本
uv run example.py
uv run --with rich example.py  # 指定未安装的依赖项
uv run --with 'rich>12,<13' example.py  # 指定依赖项版本

# 携带参数
uv run -- flask run -p 3000

# 指定python版本运行
uv run --python 3.10 example.py

# 锁定依赖项
uv lock --script example.py
```
