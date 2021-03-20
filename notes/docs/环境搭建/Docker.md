# Docker

## 概念

- Docker Registry（比如Docker Hub）：镜像仓库，每个仓库可以有不同的标签（版本），可通过`<仓库名>:<标签>`的格式指定
- Images：镜像，可理解为类或模版
- Container：容器，可理解为镜像的实例

## 镜像加速

从Docker Hub拉取镜像比较慢，可以配置镜像加速器：获取[阿里云镜像加速器](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)地址，根据说明进行配置即可。

检查加速器是否生效：执行`docker info`命令，查看Registry Mirrors字段信息。