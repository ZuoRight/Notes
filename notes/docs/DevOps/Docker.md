# Docker

[参考手册](https://docs.docker.com/reference/){ .md-button .md-button--primary }

[安装](https://docs.docker.com/engine/install/)

[以Python语言快速开始](https://docs.docker.com/language/python/)

Docker必须部署在Linux内核的系统上，Docker Desktop 是 Docker 在 Windows 10 和 macOS 操作系统上的官方安装方式，这个方法依然属于先在虚拟机中安装 Linux 然后再安装 Docker 的方法。

Docker Desktop for Windows 所使用的虚拟机是微软开发的虚拟机：Hyper-V（仅适用于 Win10），类似于 VMWare 或 VirtualBox。

安卓模拟器使用的什么虚拟机？

虚拟机技术

- docker compose
- docker swarm（相当于简化版k8s）

简单地说，容器只是你机器上的另一个进程，它与主机上的所有其他进程隔离开来。这种隔离利用了内核命名空间和 cgroups，这些特性已经在 Linux 中存在了很长时间。Docker 一直致力于使这些功能易于使用。

## 概念

- 镜像(Images)：包含运行软件所需的一切要素（代码和运行时）
- 容器(Container)：镜像的实例，每一个 Docker 容器都拥有自己的文件系统、网络体系（因此也拥有自己的 IP 地址）、进程空间以及面向 CPU 和内存定义的资源限制。同时，它不需要引导操作系统，可以即时启动。简而言之，Docker 的宗旨是隔离，即隔离主机操作系统的资源，虚拟化则是在主机操作系统上提供访客操作系统。
- 镜像仓库：比如Docker Hub，每个仓库可以有不同的标签(版本)，可通过`<仓库名>:<标签>`的格式指定

## 镜像加速

从Docker Hub拉取镜像比较慢，可以配置[阿里云镜像加速器](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)，获取到自己的加速地址后如图配置即可。

![20210726135953](http://image.zuoright.com/20210726135953.png)

检查加速器是否生效：`docker info`（查看Registry Mirrors字段信息）

## 基本命令

```bash
docker  # 查看所有命令选项
docker <command> --help  # 查看命令使用帮助
docker inspect  # 检查docker配置信息等
```

- 镜像

```bash
docker pull ubuntu  # 拉取镜像

docker images  # 查看镜像
docker rmi <镜像>  # 删除镜像

# 运行容器
"""
-d 后台运行并返回容器ID
--name="name"，指定容器名称
--net="bridge/host/none/container" 指定容器的网络连接类型
--rm 容器退出时自动清理容器内部文件，与-d同用无意义，只适用于前台运行时
"""
docker run [-d] ubuntu  # 不加tag默认执行latest版本的
docker run -it ubuntu:15.10 /bin/bash  # 运行并进入容器，-i交互，-t指定伪终端
```

- 容器

```bash
# 查看运行了哪些容器及状态等信息
# -a显示所有(包括未运行)，-q仅显示容器id，-f过滤
docker ps [-a] [-q] [-f key]
docker ps -aq -f ancestor=<镜像id/name>
docker ps -aqf exited=0

docker rm -f <容器 ID>  # 删除容器
docker rm $(docker ps -aq)  # 删除所有容器
docker rm $(docker ps -aqf ancestor=镜像名/id)  # 删除由某个镜像创建的所有容器

docker logs -f <容器 ID>  # 查看容器内的标准输出
docker top <容器 ID>  # 查看容器进程

docker start <容器 ID>  # 启动容器
docker restart <容器 ID>  # 重启
docker stop <容器 ID>  # 停止

docker exec -it <容器 ID> /bin/bash  # 进入运行中的容器

docker export <容器 ID> > ubuntu.tar  # 导出容器
docker import http://example.com/exampleimage.tgz example/imagerepo  # 导入容器
```

- 网络

```bash
# 运行web容器，-p指定端口映射到容器内服务的默认端口，或-P随机指定端口映射
# 映射后可以使用宿主机IP+4000访问
# 如果不映射则需要使用容器ip+80访问
docker run -d [-p <IP>:4000:80]/[-P] <镜像> <命令>

docker port <容器 ID> <端口>  # 查看容器端口映射

docker network create -d bridge test-net  # 网桥
```

## 构建镜像

```bash
docker commit -m="提交信息" -a="作者" <容器 ID> <目标镜像名:tag>  # 从现有镜像（可以做一些定制）fork出一个镜像

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
```

## 挂载数据卷

- `Dockerfile`中`VOLUME`指定

```Dockerfile
# 为了保证Dockerfile的可一致性，VOLUME指令挂载时不能指定宿主机目录，默认与容器路径一致
VOLUME <路径>
VOLUME ["<路径1>", "<路径2>"...]
```

- `--volume/-v`命令指定

```bash
# 将宿主机路径（绝对路径）挂载到容器，不存在会自建，存在如果有数据则会覆盖
docker run -v $(pwd)/tmp:/app/data <image>
```

- `--mount`命令指定

```Dockerfile
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

# 宿主机路径如果不存在则报错
docker run -it --mount type=volume,source=hello,target=/path <image> /bin/bash
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