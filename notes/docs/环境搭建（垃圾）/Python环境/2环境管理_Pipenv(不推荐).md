# # 环境管理_Pipenv(不推荐)

由requests库的作者Kenneth Reitz基于pip和virtualenv所编写
P
参考：<https://www.liujiangblog.com/blog/18/>

安装：

- Windows：`pip install pipenv`
- Mac：`brew install pipenv`

命令格式：`pipenv [options] command [args]`

## 初始化项目

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

## 安装项目依赖

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

## 运行项目

`pipenv run python xxx.py`

如果python命令比较长，可以在Pipfile中添加`[scripts]`节点，然后创建一个别名，比如：

`alias = "python3 manage.py runserver 0.0.0.0:8000"`

然后执行`pipenv run alias`即可

当然还可以`pipenv shell`进入到虚拟环境中执行，退出：`exit`。