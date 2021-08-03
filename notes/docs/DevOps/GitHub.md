# GitHub

## Gist

存放代码片段

## [Pages](https://docs.github.com/cn/pages)

- 解析域名

访问 <http://www.zuoright.com> 会被重定向到 <http://zuoright.github.io>，然后会被重

![20210725115345](http://image.zuoright.com/20210725115345.png)

- 设置自定义域名

![20210725120036](http://image.zuoright.com/20210725120036.png)

- 访问测试

![20210725122020](http://image.zuoright.com/20210725122020.png)

## [Actions](https://docs.github.com/cn/actions)

- workfolw
- jobs
- step
- action

CI配置文件：以`.yml`格式存放在`.github/workflows/`中，可包含多个

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

引用GitHub Actions市场中的Actions：<https://github.com/marketplace?type=actions>

可在仓库根目录下的`action.yml`中查到actions自定的输入或输出字段

当前仓库及分支：{owner}/{repo}@{ref}

还可以引用docker hub中的actions