# 数据挂载

> Docs: <https://docs.docker.com/storage/>

Docker 容器默认是不具备持久化存储能力的，容器中的数据修改操作，大多是基于写入时复制（Copy-on-Write）策略来实现的，容器会利用OverlayFS(叠加式文件系统)的特性，当用户对镜像进行修改时，自动将变更的内容写入到独立区域，再与原有数据叠加到一起，使其外观上看来像是“覆盖”了原有内容。

Docker 把解决如何访问存储的功能模块称为存储驱动（Storage Driver）

通过 `docker info` 命令可以查看到当前支持的存储驱动，比如内置的主流的 OverlayFS 驱动（Overlay、Overlay2、AUFS、BTRFS、ZFS等）

为了支持快速迭代的云计算厂商的存储系统，Docker 提出了 Volume Driver（卷驱动）的概念。用户可以通过 `docker plugin install` 命令安装外部的卷驱动。

如果想持久化存储，需要将容器挂载到系统，Docker 支持三种挂载(mount)类型

![20220109200730](http://image.zuoright.com/20220109200730.png)

> mount 是动词，表示将某个外部存储挂载到系统中  
> Volume 是名词，表示物理存储的逻辑抽象，目的是为物理存储提供有弹性的分割方式

Bind Mount 是 Docker 最先支持的挂载类型，作用是把宿主机的某个路径挂载到容器的指定路径下。Bind Mount 的设计里，Docker 只有容器的控制权，存放容器生产数据的主机目录是完全独立的，与 Docker 没有任何关系，既不受 Docker 保护，也不受 Docker 管理。数据很容易被其他进程访问到，甚至是被修改和删除。如果用户想对挂载的目录进行备份、迁移等管理运维操作，也只能在 Docker 之外靠管理员人工进行，这都增加了数据安全与操作意外的风险。

如果容器生成非持久状态数据，可以考虑使用 tmpfs 挂载到内存以避免将数据永久存储在任何地方，并通过避免写入容器的可写层来提高容器的性能。

Volume 是后来新增的挂载方式，是默认并且推荐的方式，因为它完全由 Docker 管理，更容易备份和迁移，性能更高

```bash
docker volume ls  # 列出所有数据卷
docker volume inspect hello  # 检查数据卷
docker volume create hello  # 创建数据卷，不指定卷驱动类型，默认就是local
"""
数据卷默认存储在宿主机的 /var/lib/docker/volumes/ 目录之中
在Mac/Windows系统，由于Docker都是安装在虚拟机中的，需要进入虚拟机中查找这个目录
"""
docker volume rm hello  # 删除数据卷
docker volume prune  # 删除所有未使用的卷
```

```bash
docker run --mount type=<挂载类型>,src=<卷名称或宿主机路径>,dst=<容器路径> <image:tag>
"""
type=volume 默认
  source, 或 src
    命名卷，source=volume_name，不存在会自建
    匿名卷，source可省略
  destination, 或 dst, 或 target
    容器中的路径，如果非空，会被复制到卷中，原内容不受影响
  权限
    rw: 可读可写(默认)
    ro 或 readonly: 可以防止容器修改文件

type=bind
  src: 宿主机绝对路径，不存在会自建
  dst: 容器中的路径，如果非空，会被覆盖

type=tmpfs
  没有src，实际是保存在主机内存中，容器停止时自动移除
"""
```

volume 和 bind 方式还可以用简写命令来创建，但不支持与`docker services`一起使用

```bash
docker run -v volume_name:/app/data <image:tag>  # type=volume
docker run -v "$(pwd)"/xxx:/app/data <image:tag>  # type=bind
```

tmpfs 还可以使用单独命令创建

```bash
docker run -d -it --name tmptest --tmpfs /app nginx:latest
```
