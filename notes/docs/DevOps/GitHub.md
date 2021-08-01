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

## Actions部署Pages

zuoright.github.io

- master/main分支：存放.md源文件
- blog分支：存放转换后用于pages的静态文件

上面两个分支也可以反过来，也可以分成两个仓库，把源码仓库私有化，看心情