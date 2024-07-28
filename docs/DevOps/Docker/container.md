
# 容器

## 查看容器

```shell
# 默认只显示运行的
docker ps  # 或者 docker container ls
'
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                     PORTS     NAMES
5d3804c67526   ee_test   "bash"   23 minutes ago   Exited (0) 3 minutes ago             test
'
# 参数
'
-a 显示所有容器
-q 仅显示容器ID
-f key 过滤
  docker ps -aq -f ancestor=<镜像id/name>
  docker ps -aqf exited=0
--no-trunc 参数可以查看更完整的信息，即不会截断显示，比如完整ID和COMMAND等
'

docker stats <container_id>  # 查看容器实时资源占用
docker logs [-f] <container_id>  # 查看容器日志，-f实时打印
docker top <container_id>  # 查看容器进程
docker diff <container_id>  # 查看容器文件改动

# 重命名
docker rename name_old name_new
```

## 操作容器

```shell
# 完整的container_id有64个字符，可以只写前三位
docker start <container_id>  # 启动容器
docker restart <container_id>  # 重启
docker stop <container_id>  # 停止
docker pause <container_id>  # 暂停
docker unpause <container_id>  # 继续

# 删除容器
docker rm <container_id>
sudo docker rm -f <container_id>  # rm -f 强制删除正在运行的容器
sudo docker rm $(sudo docker ps -aqf exited=0)  # 删除所有Exited(0)状态的容器
sudo docker rm $(sudo docker ps -aqf ancestor=镜像名/id)  # 删除由某个镜像创建的所有容器

# 导出容器
docker export <container_id> > ubuntu.tar
# 导入容器
docker import http://example.com/exampleimage.tgz example/imagerepo
```

## 运行容器

> 容器共享了宿主机的内核

```shell
docker run --name test 镜像名:tag  # 不加tag默认执行latest版本的镜像
docker create ...  # 创建容器但不运行，用法同run
'
--name 指定容器名称

-e var 设置单值环境变量
--env key=value 设置k-v形式的环境变量

–restart=no  默认，不会自动重启
–restart=always 开机启动，失败也会一直重启，web容器一般用这个
–restart=on-failure:10 表示出错后重启，可以限制重启次数
'
# 运行容器并与之交互
'
-i 交互
-t 指定伪终端

-d/--detach 后台运行，默认返回容器ID，或者返回命令输出
--rm 容器退出时自动清理容器内部文件
'

sudo docker run --name demo01 python:3.9 bash  # 返回空，容器状态Exited
sudo docker run --name demo02 python:3.9 "python"  # 返回空，容器状态Exited
sudo docker run --name demo03 python:3.9 "python --version"  # 返回Python 3.9.13，容器状态Exited

sudo docker run -it --name demo11 python:3.9 bash  # 容器前台运行，退出后容器状态Exited
sudo docker run -it --rm python:3.9 bash  # 容器前台运行，退出后容器自动删除，此时没必要指定--name，随机就好

sudo docker run -dit --name demo13 python:3.9 bash  # 容器后台运行，容器状态UP
sudo docker exec -it demo13 bash  # 与运行中的容器进行交互，退出后容器还是UP状态
sudo docker attach demo13  # 将运行中的后台放到前台，退出后容器变为Exited状态
```

- 还原启动容器时的参数

```shell
# 查看镜像或容器的元信息
docker inspect <container_id>
docker inspect -f='{{ .NetworkSettings.IPAddress }}' <container_id>  # -f 过滤信息，获取容器IP

'''
虽然可以通过docker inspect 分析json内容，但是不是很方便
此时可以借助第三方工具runlike
  pip安装：sudo pip install runlike
    如果没有pip就先安装pip：sudo apt install python3-pip
  或者直接docker运行：alias runlike="docker run --rm -v /var/run/docker.sock:/var/run/docker.sock assaflavie/runlike"
'''
runlike -p <container_id>
```

- 复制数据

```shell
# 复制文件：从宿主机到容器
docker cp path/file <container_id>:path
# 复制文件：从容器到宿主机
docker cp <container>:path/file .
```

## DinD

DinD, Docker inside Docker

`docker pull docker:dind`

广泛应用于 CI/CD 工作流程，它可以创建用于构建、测试和部署应用程序的独立的一次性环境，从而实现更快、更可靠的自动化管道。

在 Docker 中运行 Docker 允许开发人员创建专门为其应用程序量身定制的隔离环境。这可确保依赖项、配置和运行时环境在不同的开发阶段保持一致，从而更轻松地重现和调试问题。
