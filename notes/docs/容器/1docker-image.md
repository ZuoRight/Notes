# Docker 镜像

容器镜像，也叫作：rootfs。它只是一个操作系统的所有文件和目录，并不包含内核，最多也就几百兆。

> 传统虚拟机的镜像大多是一个磁盘的“快照”，磁盘有多大，镜像就至少有多大

## 获取镜像

- 从镜像仓库拉取镜像

```bash
docker search <image>  # 从镜像库查找镜像
docker pull <image>  # 拉取镜像

docker images  # 查看本地镜像

# 删除本地镜像，如果同一个镜像有多个标签，则不能使用id删除
docker rmi <image>
```

- 从Dockerfile构建一个镜像

[以Python语言快速开始](https://docs.docker.com/language/python/)

> 可以创建`.dockerignore`文件（语法与`.gitignore`类似）配置build规则，忽略那些不希望被打包的文件
>
> Docker build出来的镜像遵循OCI标准，可以被其它容器技术使用，比如Kubernetes、Kata

```bash
'''
-f 默认会自动查找叫Dockerfile的文件，也可以用-f指定文件名
-t/--tag，指定镜像标签，<image_name>默认为None，<tag>默认为latest
一定不要忘了最后的「.」（构建上下文），表示当前路径（默认为Dockerfile所在目录）下所有内容都会被`Sending build context to Docker daemon`
'''
docker build [-t <image_name>:<tag>] .

docker tag <image_id> <image_name>:<tag>  # 添加镜像名
docker tag <image_name>:<tag> <image_name_new>:<tag_new>  # 更改镜像名

docker history <image>  # 回放完整的镜像构建过程
docker inspect <image>  # 查看镜像信息，比如分层：RootFS.Layers
```

- 从运行中的容器fork出一个镜像，一般不推荐

```bash
docker commit -m="提交信息" -a="作者" <容器ID> <目标镜像名:tag>
```

## 镜像仓库

默认的镜像仓库，即官方提供的[Docker Hub](https://hub.docker.com)，也是最大的镜像仓库

> Github的镜像库：ghcr.io  
> Google的镜像库：gcr.io

由于镜像仓库的镜像鱼龙混杂，拉取时要检查来源，下载量，更新时间，标签等

- Official image，Docker官方提供的，大概100多个，都经过了严格的漏洞扫描和安全检测，有专门的团队负责审核、发布和更新
- Verified publisher，各认证厂商提供的镜像
- 半官方，由于认证需要交钱，有些厂商没有认证，比如OpenResty
- 非官方，大多未经过质量测试，且名字很多重复，拉取时可带上用户名

> Alpine、CentOS 的命名比较简单明了，就是数字的版本号，比如 alpine3.15  
> 而 Ubuntu、Debian 则采用了代号的形式。比如 Ubuntu 20.04 是 focal  
> 有的标签还会加上 slim、fat，通常 slim 镜像会比较小，运行效率高，而 fat 镜像会比较大，适合用来开发调试

### 拉取镜像加速

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

## 存储镜像

可以上传到Docker Hub，先注册一个账号，比如zuoright

> Docker 官方镜像也有用户名：library，但一般省略不写

```bash
# 登陆
docker login -u zuoright
# 给镜像起一个完整的名字：仓库名/镜像名:标签
docker tag demo zuoright/demo:1.0
# 把镜像推送到dockerhub
docker push zuoright/demo:1.0
```

也可以自建私有Registry，有很多成熟的解决方案，比如 Docker Registry，还有 CNCF Harbor

还可以镜像压缩传输共享

```bash
docker save <image>:<tag> -o xxx.tar  # 导出成镜像压缩包
docker load -i xxx.tar  # 导入镜像压缩包
```

## Dockerfile

Docker 在镜像的设计中，引入了层（layer）的概念，每一行指令都会生成一个只读的层(layer)，即使是设置环境变量的ENV等指令，也会生成一个空层

同一个layer可以被多个不同的镜像共享，每次构建只会涉及改动的layer，也就是一个增量 rootfs联合挂载在一个统一挂载点上。

> 用到了一种叫作联合文件系统（Union File System）的能力
>
> 所以建议测试时可以分层多一些，但发布镜像的时候尽量精简合并指令，避免镜像过于臃肿

```Dockerfile
'''
指令不区分大小写，但习惯大写
'''

# 首先要指定基础镜像
FROM python:3-slim
'''
基础镜像通常是Linux，但不同场景可以选择不同的发行版
    比如alpine，小巧且安全，但如果程序需要调用外部依赖，比如C库等，则不建议用这个
我们并不需要总是指定最基础的Linux，那样还需要自己安装python，我们可以直接指定python
    FROM ubuntu:latest
    RUN apt-get update -yRUN apt-get install -y python-pip python-dev build-essential
'''

# 创建一个工作目录，作为所有后续命令相对的根路径
WORKDIR /usr/src/app

# 从上下文目录中复制文件或者目录到容器里指定路径
COPY 源路径 目标路径
# 与COPY功能相同，会自动解压压缩包文件然后复制到目标路径
ADD 源路径 目标路径

# 最重要的指令RUN，在docker build时运行
# 可以执行任意的 Shell 命令，比如更新系统、安装应用、下载文件、创建目录、编译程序等等
RUN command
'
shell形式: RUN <command>
exec形式: RUN ["executable", "param1", "param2"]

如果要执行多个命令可以这样写
RUN command a \
    && command b \
    && command c \

但不利于调试和修改，建议放到脚本文件中用COPY引入执行脚本
COPY setup.sh /tmp
RUN cd /tmp \
    && chmod +x setup.sh \
    && ./setup.sh \
    && rm setup.sh  # 执行完后可以删除
'

# 在docker run时运行的命令
CMD echo "hello world"
: '
shell形式: CMD command param1 param2
exec形式: CMD ["executable", "param1", "param2"]  # 会被解析为json，所以必须用双引号
exec形式(与ENTRYPOINT共用): CMD ["param1","param2"]

如果有多个CMD指令仅最后一行生效

ENTRYPOINT与CMD类似，可与CMD共用，等价于：docker run xxx ENTRYPOINT CMD
    比如：nginx -c /etc/nginx/nginx.conf
        ENTRYPOINT ["nginx", "-c"]
        CMD ["/etc/nginx/nginx.conf"]

docker run 的时候如果带了命令，会覆盖CMD，但不会覆盖ENTRYPOINT
'

# 设置环境变量
# 仅在docker build的过程中有效
ARG key=value key2=value2
# 在镜像构建和容器运行时都有效
ENV key=value key2=value2

# 容器对外提供的端口号，默认为tcp
EXPOSE port1 port2
EXPOSE 80/udp

# 指定执行后续命令的用户和用户组（必须是已存在的）
USER <用户名>[:<用户组>]

# 添加一些元数据，比如作者
LABEL image.authors="7c"

# 为了保证Dockerfile的可一致性，VOLUME指令挂载时不能指定宿主机目录，默认与容器路径一致
VOLUME <路径>
VOLUME ["<路径1>", "<路径2>"...]
```

- 示例1

busybox是一个瑞士军刀式的Linux工具箱

```dockerfile
# Dockerfile.busybox
FROM busybox
CMD echo "hello world"

# docker build -f Dockerfile.busybox .
'''
Sending build context to Docker daemon   7.68kB
Step 1/2 : FROM busybox
 ---> d38589532d97
Step 2/2 : CMD echo "hello world"
 ---> Running in c5a762edd1c8
Removing intermediate container c5a762edd1c8
 ---> b61882f42db7
Successfully built b61882f42db7
'''
```

等价于

```bash
docker pull busybox      
docker run busybox echo hello world
```

- 示例2

```dockerfile
ARG IMAGE_BASE="nginx"
ARG IMAGE_TAG="1.21-alpine"

FROM ${IMAGE_BASE}:${IMAGE_TAG}

COPY ./default.conf /etc/nginx/conf.d/

RUN cd /usr/share/nginx/html \
    && echo "hello nginx" > a.txt

EXPOSE 8081 8082 8083

# docker build -t ngx-app:1.0 .
```
