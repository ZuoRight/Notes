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
