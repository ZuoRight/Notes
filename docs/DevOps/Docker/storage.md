# 存储

> Docs: <https://docs.docker.com/storage/>

Docker 容器默认是不具备持久化存储能力的，容器中的数据修改操作，大多是基于写入时复制（Copy-on-Write）策略来实现的

利用 OverlayFS(叠加式文件系统) 的特性，当用户对镜像进行修改时，自动将变更的内容写入到独立区域，再与原有数据叠加到一起，使其外观上看来像是覆盖了原有内容。

Docker 通过存储驱动（Storage Driver）访问存储数据

```shell
# 查看到当前支持的存储驱动
docker info
'
比如内置的主流 OverlayFS 驱动：Overlay、Overlay2、AUFS、BTRFS、ZFS 等
为了支持快速迭代的云计算厂商的存储系统，Docker 提出了卷驱动(Volume Driver)的概念
'
# 安装外部的卷驱动
docker plugin install
```

Docker 有如下几种挂载方式

![20220109200730](https://image.zuoright.com/20220109200730.png)

mount 挂载，动词，表示将某个外部存储挂载到容器

volume 卷，名词，一种弹性的逻辑存储方式

## tmpfs mount

临时文件系统，保存在主机内存中，性能高，容器停止时自动移除

```shell
# 方式1
docker run --mount type=tmpfs
# 方式2
docker run -d -it --name tmptest --tmpfs /app nginx:latest
```

## bind mount

是 Docker 最先支持的挂载类型，作用是把宿主机的某个路径挂载到容器的指定路径下。

存放容器生产数据的主机目录是完全独立的，与 Docker 没有任何关系，既不受 Docker 保护，也不受 Docker 管理

即 Docker 只有容器的控制权，没有存储路径的控制权，数据很容易被其他进程访问到，甚至是被修改和删除。

如果用户想对挂载的目录进行备份、迁移等管理运维操作，也只能在 Docker 之外靠管理员人工进行，这都增加了数据安全与操作意外的风险。

```shell
# 方式1
docker run --mount type=bind,src=宿主机绝对路径,dst=容器路径 image:tag
'
src 或 source: 宿主机绝对路径，不存在会自建
dst 或 destination 或 target: 容器中对应的路径，如果非空，会被覆盖
'

# 方式2，不支持与 docker services 一起使用
docker run -v "$(pwd)"/xxx:/app/data image:tag
```

## volume

是 Docker 最新支持且默认的挂载方式，推荐

因为它完全由 Docker 管理，更容易备份和迁移，性能更高

数据卷默认存储在宿主机的 `/var/lib/docker/volumes/` 路径下

在 Mac 或 Windows 系统，由于 Docker 都是安装在虚拟机中的，需要进入虚拟机中查找这个目录

```shell
# 创建数据卷，不指定卷驱动类型，默认就是 local
docker volume create hello

docker volume ls  # 列出所有数据卷
docker volume inspect hello  # 查看某个数据卷

docker volume rm hello  # 删除数据卷
docker volume prune  # 删除所有未使用的卷
```

创建卷并挂载到容器内的某个路径

```shell
# 方式1
docker run --mount type=volume,src=卷名称,dst=容器路径 image:tag
'
src
  匿名卷，source 可省略
  命名卷，source=volume_name，不存在会自建
dst
  容器中对应的路径，如果非空，会被复制到卷中，原内容不受影响
权限
  rw: 可读可写(默认)
  ro 或 readonly: 可以防止容器修改文件
'

# 方式2，不支持与 docker services 一起使用
docker run --volume/-v volume_name:/app/data image:tag
```
