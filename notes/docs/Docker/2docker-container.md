
# Docker 容器

- 运行容器

> 容器共享了宿主机的内核

```bash
docker run --name test <镜像名>:tag  # 不加tag默认执行latest版本的镜像
docker create ...  # 创建容器但不运行，用法同run
"""
--name <name>，指定容器名称

-e var 设置单值环境变量
--env key=value 设置k-v形式的环境变量

–restart=no  默认，不会自动重启
–restart=always 开机启动，失败也会一直重启，web容器一般用这个
–restart=on-failure:10 表示出错后重启，可以限制重启次数
"""

# 运行容器并与之交互
'''
-i 交互
-t 指定伪终端

-d 后台运行，默认返回容器ID，或者返回命令输出
--rm 容器退出时自动清理容器内部文件
'''
sudo docker run --name demo01 python:3.9 "/bin/bash"  # 返回空，容器状态Exited
sudo docker run --name demo02 python:3.9 "python"  # 返回空，容器状态Exited
sudo docker run --name demo03 python:3.9 "python --version"  # 返回Python 3.9.13，容器状态Exited

sudo docker run -it --name demo11 python:3.9 "/bin/bash"  # 容器前台运行，退出后容器状态Exited
sudo docker run -it --rm --name demo12 python:3.9 "/bin/bash"  # 容器前台运行，退出后容器自动删除

sudo docker run -dit --name demo13 python:3.9 "/bin/bash"  # 容器后台运行，容器状态UP
sudo docker exec -it demo13 "/bin/bash"  # 与运行中的容器进行交互，退出后容器还是UP状态
sudo docker attach demo13  # 将运行中的后台放到前台，退出后容器变为Exited状态
```

- 还原启动容器时的参数

```bash
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

- 操作容器

```bash
# 完整的container_id有64个字符，可以只写前三位
docker start <container_id>  # 启动容器
docker restart <container_id>  # 重启
docker stop <container_id>  # 停止
docker pause <container_id>  # 暂停
docker unpause <container_id>  # 继续
```

- 查看容器

```bash
# -a 显示所有容器(包括未运行的)
# --no-trunc 参数可以查看更完整的信息，即不会截断显示，比如完整ID和COMMAND等
docker ps [-a] [-f key]
# 或者
docker container ls
'''
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                     PORTS     NAMES
5d3804c67526   ee_test   "/bin/bash"   23 minutes ago   Exited (0) 3 minutes ago             test
'''
# -q 仅显示容器ID
docker ps -a -q
"""
5d3804c67526
"""
# ps -f 过滤
docker ps -aq -f ancestor=<镜像id/name>
docker ps -aqf exited=0

docker stats <container_id>  # 查看容器实时资源占用
docker logs [-f] <container_id>  # 查看容器日志，-f实时打印
docker top <container_id>  # 查看容器进程
docker diff <container_id>  # 查看容器文件改动
```

- 删除容器

```bash
# 删除所有Exited(0)状态的容器
sudo docker rm $(sudo docker ps -aqf exited=0)
# 删除由某个镜像创建的所有容器
sudo docker rm $(sudo docker ps -aqf ancestor=镜像名/id)
# rm -f 强制删除正在运行的容器
sudo docker rm -f <container_id>
```

- 存储容器

```bash
# 导出容器
docker export <container_id> > ubuntu.tar
# 导入容器
docker import http://example.com/exampleimage.tgz example/imagerepo
```

- 复制数据

```bash
# 复制文件：从宿主机到容器
docker cp path/file <container_id>:path
# 复制文件：从容器到宿主机
docker cp <container>:path/file .
```

## 数据共享

虽然可以通过cp命令实现宿主机与容器交换数据，但比较麻烦，而且容器中产生的数据无法持久化存储

这时候就需要用到绑定挂载(bind mount)机制，将宿主机目录挂载到容器中的某个路径，实现数据共享

```bash
# 默认可读可写:rw，改为:ro可以防止容器修改文件
sudo docker run -v <host_path>:<container_path>:rw ...
```

数据卷的挂载点默认是宿主机的/var/lib/docker/volumes目录

> Mac/Windows系统由于docker都是安装在虚拟机中的，需要进入虚拟机中查找这个目录
>
> 容器默认是不具备持久化存储能力的，容器中的数据修改操作，大多是基于写入时复制（Copy-on-Write）策略来实现的，容器会利用叠加式文件系统（OverlayFS）的特性，在用户意图对镜像进行修改时，自动将变更的内容写入到独立区域，再与原有数据叠加到一起，使其外观上看来像是“覆盖”了原有内容。

```bash
# 创建数据卷
docker volume create hello
# 查看数据卷
docker volume inspect hello
# 删除数据卷
docker volume rm hello
# 列出所有数据卷
docker volume ls
```

Docker 内建支持了三种挂载(mount)类型，最常用的是volume

> mount 是动词，表示将某个外部存储挂载到系统中  
> volume 是名词，表示物理存储的逻辑抽象，目的是为物理存储提供有弹性的分割方式

![20220109200730](http://image.zuoright.com/20220109200730.png)

### tmpfs

`docker run --mount type=tmpfs nginx:latest`

用于在内存中读写临时数据

### Bind Mount

```bash
# 虽然v是volume的简写，但-v指的是bind模式
# 将宿主机路径（绝对路径）挂载到容器，不存在会自建，存在如果有数据则会覆盖
docker run -v $(pwd)/tmp:/app/data <image:tag>
# 或者
docker run --mount type=bind, source=path <image:tag>
```

Bind Mount 是 Docker 最早提供的（发布时就支持）挂载类型，作用是把宿主机的某个目录（或文件）挂载到容器的指定目录（或文件）下

Bind Mount 的设计里，Docker 只有容器的控制权，存放容器生产数据的主机目录是完全独立的，与 Docker 没有任何关系，既不受 Docker 保护，也不受 Docker 管理。数据很容易被其他进程访问到，甚至是被修改和删除。如果用户想对挂载的目录进行备份、迁移等管理运维操作，也只能在 Docker 之外靠管理员人工进行，这都增加了数据安全与操作意外的风险。

### Volume

```bash
# 宿主机路径如果不存在则报错
docker run -it --mount type=volume,source=hello,target=/path <image:tag> /bin/bash
```

Docker 把解决如何访问存储的功能模块称为存储驱动（Storage Driver）。通过`docker info`命令，你能查看到当前 Docker 所支持的存储驱动。虽然内置了主流的OverlayFS 驱动，譬如 Overlay、Overlay2、AUFS、BTRFS、ZFS等，但为了支持快速迭代的云计算厂商的存储系统，Docker 提出了与 Storage Driver 相对应的 Volume Driver（卷驱动）的概念。用户可以通过`docker plugin install`命令安装外部的卷驱动，并在创建 Volume 时指定一个与其存储系统相匹配的卷驱动，如果创建 Volume 时不指定卷驱动，那默认就是 local 类型，在 Volume 中存放的数据会存储在宿主机的`/var/lib/docker/volumes/`目录之中。
