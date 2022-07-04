# Docker 镜像

```bash
docker images  # 查看本地镜像

docker search <镜像>  # 从镜像库查找镜像
docker pull <镜像>  # 拉取镜像

docker save <镜像> > <镜像>.tar  # 导出镜像
docker load < <镜像>.tar  # 导入镜像

docker rmi <镜像>  # 删除镜像

docker tag <镜像>:taga <镜像>:tagb  # 更改镜像名

docker history <镜像>  # 查看镜像创建历史
```

## 构建镜像

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

## Dockerfile

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
