# Docker 存储

Volume 机制，允许你将宿主机上指定的目录或者文件，挂载到容器里面进行读取和修改操作。

```bash
# 没有显示声明宿主机目录，那么 Docker 就会默认在宿主机上创建一个临时目录 /var/lib/docker/volumes/[VOLUME_ID]/_data，然后把它挂载到容器的 /test 目录上
$ docker run -v /test ...
# Docker 就直接把宿主机的 /home 目录挂载到容器的 /test 目录上
$ docker run -v /home:/test ...
```

这里要使用到的挂载技术，就是 Linux 的绑定挂载（bind mount）机制。它的主要作用就是，允许你将一个目录或者文件，而不是整个设备，挂载到一个指定的目录上。并且，这时你在该挂载点上进行的任何操作，只是发生在被挂载的目录或者文件上，而原挂载点的内容则会被隐藏起来且不受影响。

绑定挂载实际上是一个 inode 替换的过程。在 Linux 操作系统中，inode 可以理解为存放文件内容的“对象”，而 dentry，也叫目录项，就是访问这个 inode 所使用的“指针”

![20210910000628](http://image.zuoright.com/20210910000628.png)

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

## tmpfs

`docker run --mount type=tmpfs nginx:latest`

用于在内存中读写临时数据

## Bind Mount

```bash
# 虽然v是volume的简写，但-v指的是bind模式
# 将宿主机路径（绝对路径）挂载到容器，不存在会自建，存在如果有数据则会覆盖
docker run -v $(pwd)/tmp:/app/data <image:tag>
# 或者
docker run --mount type=bind, source=path <image:tag>
```

Bind Mount 是 Docker 最早提供的（发布时就支持）挂载类型，作用是把宿主机的某个目录（或文件）挂载到容器的指定目录（或文件）下

Bind Mount 的设计里，Docker 只有容器的控制权，存放容器生产数据的主机目录是完全独立的，与 Docker 没有任何关系，既不受 Docker 保护，也不受 Docker 管理。数据很容易被其他进程访问到，甚至是被修改和删除。如果用户想对挂载的目录进行备份、迁移等管理运维操作，也只能在 Docker 之外靠管理员人工进行，这都增加了数据安全与操作意外的风险。

## Volume

```bash
# 宿主机路径如果不存在则报错
docker run -it --mount type=volume,source=hello,target=/path <image:tag> /bin/bash
```

Docker 把解决如何访问存储的功能模块称为存储驱动（Storage Driver）。通过`docker info`命令，你能查看到当前 Docker 所支持的存储驱动。虽然内置了主流的OverlayFS 驱动，譬如 Overlay、Overlay2、AUFS、BTRFS、ZFS等，但为了支持快速迭代的云计算厂商的存储系统，Docker 提出了与 Storage Driver 相对应的 Volume Driver（卷驱动）的概念。用户可以通过`docker plugin install`命令安装外部的卷驱动，并在创建 Volume 时指定一个与其存储系统相匹配的卷驱动，如果创建 Volume 时不指定卷驱动，那默认就是 local 类型，在 Volume 中存放的数据会存储在宿主机的`/var/lib/docker/volumes/`目录之中。
