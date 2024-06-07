# Jupyter

<https://jupyter.org/>

Jupyter Notebook

```shell
# 安装
pip install jupyter

# 查看版本
jupyter --version

# 启动
jupyter notebook
```

## IPython

<https://ipython.org/>

IPython 基于 Python Shell 的第三方开源交互命令行，提供了自动补全、语法高亮、魔术命令、历史记录等功能，被集成到了 Jupyter 中。

可单独使用

```shell
# 安装
pip install ipython

# 或者
conda install ipython

# 启动
ipython
```

## Anaconda

Anaconda 是一个用于科学计算和数据科学的开源 Python 发行版。它简化了包管理和部署，特别适合处理大规模数据、分析和计算密集型任务。 包含了许多预安装的科学计算和数据科学包，比如 NumPy、SciPy、Pandas、Matplotlib、Jupyter 等。

> 下载安装：<https://www.anaconda.com/download/>

- 社区版：Anaconda Distribution
    - Anaconda Navigator 图形界面
    - Anaconda Cloud 包管理服务
    - Conda 包以及虚拟环境管理工具，还可以管理 Python 版本，可单独使用
- 企业版：Anaconda Enterprise
- 最小发行版：Miniconda

### Conda

Anaconda 的 Conda 工具可以轻松管理 Jupyter 的依赖和插件，并可以在不同的虚拟环境中运行 Jupyter Notebook，从而避免不同项目间的包冲突。

虽然 Conda 来自 Python 社区且由 Python 语言开发，但它不止可以管理 Python 语言及Python 的包，还可以管理其他语言的包（Java，R，Ruby，JS，Lua，Scala，C/C++，Fortran）

```shell
conda info
conda help

conda env list
conda info --envs  # 查看
conda create -n xxx python=3  # 创建
conda remove -n xxx --all  # 删除

activate xxx  # 进入
deactivate xxx  # 退出

conda install xxx
conda update xxx
conda remove xxx
```
