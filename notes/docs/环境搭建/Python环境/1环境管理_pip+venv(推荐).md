# 环境管理_pip+venv(推荐)

有以下几种选择

- pip + venv (推荐)
- pip + virtualenv + virtualenvwrapper
- pipenv
- conda

## pip

官方文档：<https://pip.pypa.io/en/stable/cli/>

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
# 搜索包
pip search xxx
# 下载包
pip download xxx

# 安装，默认安装最新版本
pip install xxx
# 指定版本
pip install xxx==version
# 指定镜像源
pip install xxx -i 镜像源 --trusted-host 镜像地址对应的host

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

## venv

```shell
# 在当前目录创建一个名为env(可自定义)的虚拟环境，删除它即删除了虚拟环境
python3 -m venv env

# 激活虚拟环境，激活后命令提示符前会显示：(env)
source env/bin/activate  # Mac/Linux
"env\Scripts\activate"  # Windows

# 安装项目依赖
pip install flask

# 退出虚拟环境
deactivate
```
