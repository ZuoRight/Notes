# Docker

[参考手册](https://docs.docker.com/reference/)

[以Python语言快速开始](https://docs.docker.com/language/python/)

Docker必须部署在Linux内核的系统上，Docker Desktop 是 Docker 在 Windows 10 和 macOS 操作系统上的官方安装方式，这个方法依然属于先在虚拟机中安装 Linux 然后再安装 Docker 的方法。

Docker Desktop for Windows 所使用的虚拟机是微软开发的虚拟机：Hyper-V（仅适用于 Win10），类似于 VMWare 或 VirtualBox。

安卓模拟器使用的什么虚拟机？

虚拟机技术

docker compose
docker swarm（相当于简化版k8s）

简单地说，容器只是你机器上的另一个进程，它与主机上的所有其他进程隔离开来。这种隔离利用了内核命名空间和 cgroups，这些特性已经在 Linux 中存在了很长时间。Docker 一直致力于使这些功能易于使用。

## 概念

- 镜像(Images)：包含运行软件所需的一切要素（代码和运行时）
- 容器(Container)：镜像的实例，每一个 Docker 容器都拥有自己的文件系统、网络体系（因此也拥有自己的 IP 地址）、进程空间以及面向 CPU 和内存定义的资源限制。同时，它不需要引导操作系统，可以即时启动。简而言之，Docker 的宗旨是隔离，即隔离主机操作系统的资源，虚拟化则是在主机操作系统上提供访客操作系统。
- 镜像仓库：比如Docker Hub，每个仓库可以有不同的标签(版本)，可通过`<仓库名>:<标签>`的格式指定

## 镜像加速

从Docker Hub拉取镜像比较慢，可以配置[阿里云镜像加速器](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)，获取到自己的加速地址后如图配置即可。

![20210726135953](http://image.zuoright.com/20210726135953.png)

检查加速器是否生效：`docker info`（查看Registry Mirrors字段信息）
