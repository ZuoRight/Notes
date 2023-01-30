# GitHub

## Actions

> Docs: <https://docs.github.com/zh/actions/using-workflows/about-workflows>

### 配置文件

路径固定，文件名称可自定义：`.github/workflows/xxx.yml`

```yaml
name: xxx  # workflow的名称，省略则默认为文件名

on:  # 触发条件
  push
    branches:  # 可限定触发分支
      - main
      - master

jobs:
  job1:  # id
    container: python  # 指定docker镜像
    runs-on: ubuntu-18.04  # 运行环境，类似GitLab的tags
    steps:
      - uses: actions/setup-python@v4  # 引用
        with:
          python-version: 3.x  # 会被设置为环境变量：INPUT_PYTHON-VERSION
      - name: desc  # 说明
        run: action  # 命令
  job2:
    needs: job1  # 依赖
    steps:
      - name:
        env:  # 环境变量
          key: value
        run: action
```

### uses

- 引用[GitHub Marketplace](https://github.com/marketplace?type=actions)
- 引用当前或其它仓库：`uses: actions/setup-python@v4`
- 引用docker hub中的actions：`uses: docker://alpine:3.8`

## 下载指定文件

[SVN](https://www.visualsvn.com/)是集中式的VCS，它修复了CVS(一款更老的集中式VCS)的一些稳定性问题，分为客户端和服务端。

日常可以使用SVN下载Github仓库指定文件

![20210824234106](http://image.zuoright.com/20210824234106.png)

e.g. Ubuntu: `apt install subversion`

![20210824233016](http://image.zuoright.com/20210824233016.png)

也可以用`svn checkout path/dir`或者`svn co path/dir`来检出文件夹，但是下载文件貌似只能用`svn export path/file`
