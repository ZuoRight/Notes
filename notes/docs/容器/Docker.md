# Docker

[参考手册](https://docs.docker.com/reference/){ .md-button .md-button--primary }

[安装](https://docs.docker.com/engine/install/)

```bash
# Ubuntu
sudo apt-get remove docker docker-engine docker.io containerd runc
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

# ubuntu安装完会自启动
systemctl start docker
# 开机自启动
systemctl enable docker
```

[以Python语言快速开始](https://docs.docker.com/language/python/)

简单地说，容器就是利用了Linux Namespace(内核命名空间)、Linux Cgroups(控制资源的)以及rootfs等早就已经存在的技术隔离出来的一个进程。

容器没有虚拟化内核，而是与宿主机共用内核，Docker必须部署在Linux内核的系统上。

> Docker Desktop for Windows/Mac 本质上是基于Hyper-V等虚拟机安装的，类似于在 VMWare 或 VirtualBox中安装。

Docker中安装的各种操作系统镜像其实只是套了壳的软件而已，并不是真正的操作系统。

> 凡是挑内核的场景都不适用于使用Docker，比如不能做系统兼容性测试，也不能在Docker环境中驱动IE浏览器。

## 概念

- 镜像(Images)：包含运行软件所需的一切要素（代码和运行时）
- 容器(Container)：镜像的实例，每一个 Docker 容器都拥有自己的文件系统、网络体系（因此也拥有自己的 IP 地址）、进程空间以及面向 CPU 和内存定义的资源限制。同时，它不需要引导操作系统，可以即时启动。简而言之，Docker 的宗旨是隔离，即隔离主机操作系统的资源，虚拟化则是在主机操作系统上提供访客操作系统。
- 镜像仓库：比如Docker Hub，镜像仓库其实也是docker启动的一个容器，然后挂载了出来。每个仓库可以有不同的标签(版本)，可通过`<仓库名>:<标签>`的格式指定

## 镜像加速

从Docker Hub拉取镜像比较慢，可以配置[阿里云镜像加速器](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)，获取到自己的加速地址后如图配置即可。

- Linux

```bash
cd /etc/docker
vim daemon.json  # 添加 registry-mirrors 配置
sudo systemctl daemon-reload  # 重新加载配置文件
sudo systemctl restart docker  # 重启docker
```

- Mac/Windows

![20210726135953](http://image.zuoright.com/20210726135953.png)

## 基本命令

```bash
docker  # 查看所有命令选项
docker <command> --help  # 查看命令使用帮助
docker version  # 版本信息
docker info  # 系统信息
```

## 容器

- 运行容器

```bash
docker start <容器ID>  # 启动容器
docker restart <容器ID>  # 重启
docker stop <容器ID>  # 停止
docker pause <容器ID>  # 暂停
docker unpause <容器ID>  # 继续

"""
--name <name>，指定容器名称

-d 后台运行并返回容器ID
--rm 容器退出时自动清理容器内部文件，与-d同用无意义，只适用于前台运行时

-e var 设置单值环境变量
--env key=value 设置k-v形式的环境变量
"""
docker run -d --name test <镜像名>:tag  # 不加tag默认执行latest版本的镜像
# 运行并进入容器，-i交互，-t指定伪终端
docker run -it --name test ubuntu:15.10 /bin/bash

# 与运行中的容器进行交互
docker exec -it <容器ID> <command>
```

- 查看容器

```bash
# -a 显示所有容器(包括未运行的)
docker ps [-a] [-f key]
"""
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                     PORTS     NAMES
5d3804c67526   ee_test   "/bin/bash"   23 minutes ago   Exited (0) 3 minutes ago             test
"""
# -q 仅显示容器id
docker ps -a -q
"""
5d3804c67526
"""
# -f 过滤
docker ps -aq -f ancestor=<镜像id/name>
docker ps -aqf exited=0

# 查看容器元信息
docker inspect <容器ID>
docker inspect -f='{{ .NetworkSettings.IPAddress }}'  <容器ID>  # -f 过滤信息，获取容器IP

docker stats <容器ID>  # 查看容器实时资源占用
docker logs [-f] <容器ID>  # 查看容器日志，-f实时打印
docker top <容器ID>  # 查看容器进程
docker diff <容器ID>  # 查看容器文件改动
```

- 删除容器

```bash
# -f 强制删除正在运行的容器
docker rm -f <容器ID>
docker rm $(docker ps -aq)  # 删除所有容器
docker rm $(docker ps -aqf ancestor=镜像名/id)  # 删除由某个镜像创建的所有容器
```

- 其他

```bash
# 从容器中复制文件到宿主机
docker cp <容器ID>:path/file .
# 从宿主机复制文件到容器中
docker cp path/file <容器ID>:path

# 导出容器
docker export <容器ID> > ubuntu.tar
# 导入容器
docker import http://example.com/exampleimage.tgz example/imagerepo
```

## 镜像

```bash
docker images  # 查看本地镜像
docker search <镜像>  # 查找镜像
docker pull <镜像>  # 拉取镜像
docker save <镜像> > <镜像>.tar  # 导出镜像
docker load < <镜像>.tar  # 导入镜像
docker rmi <镜像>  # 删除镜像
docker tag <镜像>:taga <镜像>:tagb  # 更改镜像名
docker history <镜像>  # 查看镜像创建历史
```

### 构建镜像

- 从Dockerfile构建一个镜像

```bash
# -t/-tag必须加，但可以省略标签名，默认为latest
# 一定不要忘了最后的上下文路径「.」，该路径下所有内容都会被打包，默认为Dockerfile所在目录
docker build -t/--tag <目标镜像名>[:<tag>] .
docker tag <镜像ID> <镜像名>:tag  # 打标签
```

- 从现有镜像（可以做一些定制）fork出一个镜像

```bash
docker commit -m="提交信息" -a="作者" <容器ID> <目标镜像名:tag>
```

### Dockerfile

Dockerfile 中的每个原语执行后，都会生成一个对应的镜像层。即使原语本身并没有明显地修改文件的操作（比如，ENV 原语），它对应的层也会存在。只不过在外界看来，这个层是空的。

```Dockerfile
# 指定基础镜像，免去安装各种环境的操作
FROM python:3-slim
# 否则就需要这样写了
# FROM ubuntu:latest
# RUN apt-get update -yRUN apt-get install -y python-pip python-dev build-essential

# 创建一个工作目录，作为所有后续命令相对的根路径
WORKDIR /usr/src/app

# 从上下文目录中复制文件或者目录到容器里指定路径
COPY 源路径 目标路径
# 与COPY功能相同，会自动解压压缩包文件然后复制到目标路径
ADD 源路径 目标路径

# 允许外界访问容器的80端口
EXPOSE 80

# 设置环境变量
ENV <key> <value>
ENV <key1>=<value1> <key2>=<value2>...
# 作用域仅在docker build的过程中有效
ARG

# 在docker build时运行的命令
RUN <命令行命令>
RUN ["可执行文件", "参数1", "参数2"]
RUN command a \
    && command b \
    && command c \

# 在docker run时运行的命令
# 如果有多个仅会运行最后一条，可被 docker run 命令行参数中指定要运行的程序所覆盖
# CMD ["可执行文件", "参数1", "参数2"]
CMD ["python", "app.py"]
# 等价于docker run <image> python app.py

# 运行程序
# 不会被 docker run 的命令行参数指定的指令所覆盖
ENTRYPOINT ["可执行文件", "参数1", "参数2"]

# ENTRYPOINT搭配CMD命令使用构成指令：ENTRYPOINT CMD，即 CMD 的内容就是 ENTRYPOINT 的参数
# 在不指定 ENTRYPOINT 时，默认：/bin/sh -c "python app.py"
# 比如：nginx -c /etc/nginx/nginx.conf
ENTRYPOINT ["nginx", "-c"]
CMD ["/etc/nginx/nginx.conf"]

# 指定执行后续命令的用户和用户组（必须是已存在的）
USER <用户名>[:<用户组>]

# 添加一些元数据，比如作者
LABEL image.authors="7c"

# 为了保证Dockerfile的可一致性，VOLUME指令挂载时不能指定宿主机目录，默认与容器路径一致
VOLUME <路径>
VOLUME ["<路径1>", "<路径2>"...]
```

### 发布镜像

先注册一个dockerhub账号，比如zuoright，也可以自己搭建一个统一存放镜像的系统（Docker Registry）

```bash
# 给镜像起一个完整的名字：镜像仓库名/镜像名:标签
docker tag demo zuoright/demo:v1
# 把镜像推送到dockerhub
docker push zuoright/demo:v1
# 把正在运行的容器直接提交为一个镜像
docker commit <容器ID> zuoright/demo:v2
```

## Docker Compose

有些任务需要多个容器一起提供服务，这时候一个个管理就比较麻烦了，可以用Docker Compose统一管理

在PC和Mac下的Docker Desktop默认会带Docker-Compose，无需额外安装

Linux下需要单独安装，访问：<https://github.com/docker/compose/releases>，找到对应linux系统的安装包右键复制链接

```bash
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-Linux-x86_64" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version
```
