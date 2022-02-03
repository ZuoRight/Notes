# Docker

[参考手册](https://docs.docker.com/reference/){ .md-button .md-button--primary }

[安装](https://docs.docker.com/engine/install/)

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

- 镜像

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

- 容器

```bash
# 运行容器
"""
-d 后台运行并返回容器ID
--name <name>，指定容器名称
--net="bridge/host/none/container" 指定容器的网络连接类型
--rm 容器退出时自动清理容器内部文件，与-d同用无意义，只适用于前台运行时
"""
docker run -d --name test <镜像名>  # 不加tag默认执行latest版本的镜像
docker run -it ubuntu:15.10 /bin/bash  # 运行并进入容器，-i交互，-t指定伪终端
docker exec -it <容器ID> <command>  # 与已存在的容器进行交互

docker cp <容器ID>:path/file .  # 从容器中复制文件到宿主机
docker cp path/file <容器ID>:path  # 从宿主机复制文件到容器中

docker start <容器ID>  # 启动容器
docker restart <容器ID>  # 重启
docker stop <容器ID>  # 停止
docker pause <容器ID>  # 暂停
docker unpause <容器ID>  # 继续

# 查看运行了哪些容器及状态等信息
# -a显示所有(包括未运行)，-q仅显示容器id，-f过滤
docker ps [-a] [-q] [-f key]
docker ps -aq -f ancestor=<镜像id/name>
docker ps -aqf exited=0

docker inspect <容器ID> # 查看容器元信息
docker inspect -f='{{ .NetworkSettings.IPAddress }}'  <容器ID>  # -f 过滤信息，获取容器IP

docker stats <容器ID>  # 查看容器实时资源占用
docker logs [-f] <容器ID>  # 查看容器日志，-f实时打印
docker top <容器ID>  # 查看容器进程
docker diff <容器ID>  # 查看容器文件改动

docker rm -f <容器ID>  # 删除容器，-f强制删除正在运行的容器
docker rm $(docker ps -aq)  # 删除所有容器
docker rm $(docker ps -aqf ancestor=镜像名/id)  # 删除由某个镜像创建的所有容器

docker export <容器ID> > ubuntu.tar  # 导出容器
docker import http://example.com/exampleimage.tgz example/imagerepo  # 导入容器
```

## 网络

```bash
# 运行web容器，-p指定端口映射到容器内服务的默认端口
# 映射后可以使用<宿主机IP:4000>访问
docker run -d -p <IP>:4000:80] <镜像> <命令>  # 也可以-P随机指定端口映射
# 查看容器端口映射
docker port <容器ID>
"""
80/tcp -> 0.0.0.0:8089
80/tcp -> :::8089
"""

docker network create [-d bridge] test-net  # 创建容器网络，-d指定网络模式，默认bridge桥接方式
docker network ls  # 查看所有容器网络
```

## 构建镜像

```bash
docker commit -m="提交信息" -a="作者" <容器ID> <目标镜像名:tag>  # 从现有镜像（可以做一些定制）fork出一个镜像

# 从Dockerfile构建一个镜像
# -t必须加，但tag可以缺省，默认为latest
# 一定不要忘了最后的上下文路径「.」，该路径下所有内容都会被打包，默认为Dockerfile所在目录
docker build -t/--tag <目标镜像名>[:<tag>] .
docker tag <镜像ID> <镜像名>:tag  # 打标签
```

- Dockerfile

Dockerfile 中的每个原语执行后，都会生成一个对应的镜像层。即使原语本身并没有明显地修改文件的操作（比如，ENV 原语），它对应的层也会存在。只不过在外界看来，这个层是空的。

```Dockerfile
# 指定基础镜像，免去安装各种环境的操作
FROM python:3-slim
# 否则就需要这样写了
# FROM ubuntu:latest
# RUN apt-get update -yRUN apt-get install -y python-pip python-dev build-essential

# 创建一个工作目录，作为所有后续命令相对的根路径
WORKDIR /app

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

## 发布镜像

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

## 容器存储

容器是镜像的运行时实例，为了保证镜像能够重复地产生出具备一致性的运行时实例，必须要求镜像本身是持久而稳定的，这决定了在容器中发生的一切数据变动操作都不能真正写入到镜像当中，否则必然会破坏镜像稳定不变的性质。

容器默认是不具备持久化存储能力的，容器中的数据修改操作，大多是基于写入时复制（Copy-on-Write）策略来实现的，容器会利用叠加式文件系统（OverlayFS）的特性，在用户意图对镜像进行修改时，自动将变更的内容写入到独立区域，再与原有数据叠加到一起，使其外观上看来像是“覆盖”了原有内容。

> - Mount 是动词，表示将某个外部存储挂载到系统中
>
> - Volume 是名词，表示物理存储的逻辑抽象，目的是为物理存储提供有弹性的分割方式

```bash
# 创建数据卷
# 数据卷的挂载点，默认是本机（Linux） /var/lib/docker/volumes目录
# Mac/Windows系统由于docker都是安装在虚拟机中的，需要进入虚拟机中查找这个目录
docker volume create hello
# 查看数据卷
docker volume inspect hello
# 删除数据卷
docker volume rm hello
# 列出所有数据卷
docker volume ls
```

Docker 内建支持了三种挂载(mount)类型，最常用的是volume

![20220109200730](http://image.zuoright.com/20220109200730.png)

### tmpfs

`docker run --mount type=tmpfs``nginx:latest`

用于在内存中读写临时数据

### Bind Mount

```bash
# 虽然v是volume的简写，但-v指的是bind模式
# 将宿主机路径（绝对路径）挂载到容器，不存在会自建，存在如果有数据则会覆盖
docker run -v $(pwd)/tmp:/app/data <image>
# 或者
docker run --mount type=bind, source=path nginx:latest
```

Bind Mount 是 Docker 最早提供的（发布时就支持）挂载类型，作用是把宿主机的某个目录（或文件）挂载到容器的指定目录（或文件）下

Bind Mount 的设计里，Docker 只有容器的控制权，存放容器生产数据的主机目录是完全独立的，与 Docker 没有任何关系，既不受 Docker 保护，也不受 Docker 管理。数据很容易被其他进程访问到，甚至是被修改和删除。如果用户想对挂载的目录进行备份、迁移等管理运维操作，也只能在 Docker 之外靠管理员人工进行，这都增加了数据安全与操作意外的风险。

### Volume

```bash
# 宿主机路径如果不存在则报错
docker run -it --mount type=volume,source=hello,target=/path <image> /bin/bash
```

Docker 把解决如何访问存储的功能模块称为存储驱动（Storage Driver）。通过`docker info`命令，你能查看到当前 Docker 所支持的存储驱动。虽然内置了主流的OverlayFS 驱动，譬如 Overlay、Overlay2、AUFS、BTRFS、ZFS等，但为了支持快速迭代的云计算厂商的存储系统，Docker 提出了与 Storage Driver 相对应的 Volume Driver（卷驱动）的概念。用户可以通过`docker plugin install`命令安装外部的卷驱动，并在创建 Volume 时指定一个与其存储系统相匹配的卷驱动，如果创建 Volume 时不指定卷驱动，那默认就是 local 类型，在 Volume 中存放的数据会存储在宿主机的`/var/lib/docker/volumes/`目录之中。
