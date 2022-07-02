# GitHub Actions

> <https://docs.github.com/cn/actions>
>
> 配置文件：`.github/workflows/xxx.yml` (YAML语法)

## 流水线 Workfolws

```yaml
name: xxx  # workflow的名称，省略则默认为文件名

on:  # 触发条件
  push
    branches: [main]  # 可限定触发分支

jobs:
  job1:  # id
    container: python  # 指定docker镜像
    runs-on: ubuntu-18.04  # 运行环境，类似GitLab的tags
    steps:
      - name: desc  # 说明
        run: action  # 命令
      - name: desc
        run: action
  job2:
    needs: job1  # 依赖
    steps:
      - name:
        env:  # 环境变量
          key: value
        run: action
```

## Actions

引用GitHub Actions市场中的Actions：<https://github.com/marketplace?type=actions>

可在仓库根目录下的`action.yml`中查到actions自定的输入或输出字段

当前仓库及分支：{owner}/{repo}@{ref}

还可以引用docker hub中的actions

## 下载指定文件

[SVN](https://www.visualsvn.com/)是集中式的VCS，它修复了CVS(一款更老的集中式VCS)的一些稳定性问题，分为客户端和服务端。

日常可以使用SVN下载Github仓库指定文件

![20210824234106](http://image.zuoright.com/20210824234106.png)

e.g. Ubuntu: `apt install subversion`

![20210824233016](http://image.zuoright.com/20210824233016.png)

也可以用`svn checkout path/dir`或者`svn co path/dir`来检出文件夹，但是下载文件貌似只能用`svn export path/file`
