# Docker Compose

有些任务需要多个容器一起提供服务，这时候一个个管理就比较麻烦了，可以用Docker Compose统一管理

在PC和Mac下的Docker Desktop默认会带Docker-Compose，无需额外安装

Linux下需要单独安装，访问：<https://github.com/docker/compose/releases>，找到对应linux系统的安装包右键复制链接

```bash
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-Linux-x86_64" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

## Kubernetes

随着容器化应用数量的不断增长，容器操作变得越来越复杂。如何协调、扩展、管理和调度数以百计的容器？

于是Kubernetes出现了，Kubernetes，简称k8s，是一个支持您运行 Docker 容器和负载的开源编排系统。当您需要扩展跨多台服务器部署的多个容器时，它可以为您降低操作复杂性。使用 Kubernetes 引擎，您可以自动化编排容器生命周期，将应用容器分布在整个托管式基础设施中。最后，Kubernetes 还可以快速按需扩展或收缩资源，持续供应、调度、删除和监视容器的运行状况。
