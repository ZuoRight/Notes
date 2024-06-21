# GitHub

## 常见缩写

```text
PR：Pull Request，提交PR
WIP：Work In Progress，如果你要做一个很大的改动，可以在完成部分的情况下先提交，但说明WIP，方便项目维护人员知道你还在 Work，同时他们可以先审核已经完成的。
PTAL：Please Take A Look，请求项目维护人员进行 Code Review。
TBR：To Be Reviewed，提示这些代码要进行审核。
TL;DR：Too Long; Didn't Read，太长了，懒得看。
LGTM：Looks Good To Me，通常是 Code Review 的时候回复的，即审核通过的意思。
SGTM：Sounds Good To Me，跟 LGTM 同义。
AFAIK：As Far As I Know，据我所知。
CC：Carbon Copy，抄送。
```

## GitHub Packages

是一个用于托管和管理包（包括容器和其他依赖项）的平台

为常用的包管理器提供不同的包注册表，例如 npm、RubyGems、Apache Maven、Gradle、Docker 和 NuGet

可以将 GitHub Packages 与 GitHub API、GitHub Actions 和 webhook 集成

## GitHub Actions

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

  python:
    runs-on: ubuntu-latest
    steps:
      # 检出当前代码库，克隆到 GitHub 提供的虚拟环境中
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Python runtime
        uses: actions/setup-python@v5
        with:
          python-version: ${{ env.PYTHON_VERSION }}
          cache: pip  # 缓存 pip 安装的依赖包
          cache-dependency-path: |  # 监控依赖变化的文件列表
            requirements.txt
```

### uses

- 引用 [GitHub Marketplace](https://github.com/marketplace?type=actions)
- 引用当前或其它仓库：`uses: actions/setup-python@v4`
- 引用 DockerHub 中的 actions：`uses: docker://alpine:3.8`

## GitHub Pages

> 官方文档：<https://docs.github.com/cn/pages>

- 创建入口文件

`index.html`、`index.md` 或 `README.md` 文件

- 解析域名

![20210725115345](http://image.zuoright.com/20210725115345.png)

- 设置自定义域名

![20210725120036](http://image.zuoright.com/20210725120036.png)

- 访问测试

![20210725122020](http://image.zuoright.com/20210725122020.png)

## 下载指定文件

[SVN](https://www.visualsvn.com/) 是集中式的 VCS，它修复了 CVS（一款更老的集中式VCS）的一些稳定性问题，分为客户端和服务端。

日常可以使用SVN下载Github仓库指定文件

![20210824234106](http://image.zuoright.com/20210824234106.png)

e.g. Ubuntu: `apt install subversion`

![20210824233016](http://image.zuoright.com/20210824233016.png)

也可以用 `svn checkout path/dir` 或者 `svn co path/dir` 来检出文件夹，但是下载文件貌似只能用 `svn export path/file`
