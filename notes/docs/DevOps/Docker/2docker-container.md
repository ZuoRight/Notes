
# Docker 容器

- 运行容器

```bash
docker run --name test -p <ip>:4000:80 -d <镜像名>:tag  # 不加tag默认执行latest版本的镜像
docker create ...  # 创建容器但不运行，用法同run
"""
--name <name>，指定容器名称

容器默认不会对外发布网络，需要使用`-p`（`--publish`）参数将端口映射到宿主机，供docker外部服务或未连接到此容器的其它容器使用，也可以-P随机指定端口映射

-d 后台运行并返回容器ID

-e var 设置单值环境变量
--env key=value 设置k-v形式的环境变量

--rm 容器退出时自动清理容器内部文件，与-d同用无意义，只适用于前台运行时
"""
```

- 操作容器

```bash
docker start <容器ID>  # 启动容器
docker restart <容器ID>  # 重启
docker stop <容器ID>  # 停止
docker pause <容器ID>  # 暂停
docker unpause <容器ID>  # 继续

docker run -it --name test ubuntu:15.10 /bin/bash  # 运行并进入容器，-i交互，-t指定伪终端
docker exec -it <容器ID> <command>  # 与运行中的容器进行交互
```

- 查看容器

```bash
# -a 显示所有容器(包括未运行的)
# --no-trunc 参数可以查看更完整的信息，即不会截断显示，比如完整ID和COMMAND等
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

docker stats <容器ID>  # 查看容器实时资源占用
docker logs [-f] <容器ID>  # 查看容器日志，-f实时打印
docker top <容器ID>  # 查看容器进程
docker diff <容器ID>  # 查看容器文件改动

docker port <容器ID>  # 查看容器端口映射
"""
80/tcp -> 0.0.0.0:8089
80/tcp -> :::8089
"""
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

- 还原启动容器时的参数

```bash
"""
虽然可以通过docker inspect 分析json内容，但是不是很方便
此时可以借助第三方工具runlike
  pip安装：sudo pip install runlike
    如果没有pip就先安装pip：sudo apt install python3-pip
  或者直接docker运行：alias runlike="docker run --rm -v /var/run/docker.sock:/var/run/docker.sock assaflavie/runlike"
"""
runlike -p <容器ID>
```

## 容器网络

即容器间如何通信

安装 Docker 时会自动在宿主机上创建一个名为 docker0 的网桥（虚拟交换机），并默认有三种网络模式：`bridge`、`host`、`none`

```bash
# 查看网络模式
docker network ls
"""
NETWORK ID     NAME      DRIVER    SCOPE
e01de21fde14   bridge    bridge    local
28abc4c947b8   host      host      local
d58fdf42ccb2   none      null      local
"""
```

> 虚拟网卡：veth，成对出现
>
> 虚拟交换机：Linux Bridge(网桥)，docker0，网桥是根据 MAC 地址做二层转发的，网桥不允许给接入的设备设置 IP 地址，但与物理交换机不同的是，它允许给自己设置 IP 地址。如果数据包的目的 MAC 地址为网桥本身，并且网桥有设置了 IP 地址的话，那该数据包即被认为是收到发往创建网桥那台主机的数据包，此数据包将不会转发到任何设备，而是直接交给上层（三层）协议栈去处理
>
> 虚拟路由器：Linux 内核

```bash
# 运行容器时指定网络，不指定默认为bridge桥接模式
docker run --name=demo --network=bridge -d myapp
```

## 创建网络

除了默认网路模式外，Docker还支持由用户自行创建的网络：`container`、`macvlan`、`overlay`

```bash
# 创建容器网络，默认bridge桥接模式，-d指定网络模式
docker network create [-d xxx] my-net
docker network rm my-net  # 删除

docker network connect my-net my-nginx  # 将运行中的容器my-nginx连接到my-net网络
docker network disconnect my-net my-nginx # 断开网络
```

比较常用的模式为：`bridge`、`host`、`container`

## 桥接模式

`docker run --network=bridge`

桥接模式下，Docker 会为新容器分配独立的网络名称空间，创建好 veth pair，一端接入容器，另一端接入到 docker0 网桥上。Docker 为每个容器自动分配好 IP 地址，默认配置下地址范围是 172.17.0.0/24，docker0 的地址默认是 172.17.0.1，并且设置所有容器的网关均为 docker0，这样所有接入同一个网桥内的容器直接依靠二层网络来通信，在此范围之外的容器、主机就必须通过网关来访问，具体过程笔者在介绍 Linux Bridge 时已经举例详细讲解过。

![20220105231419](http://image.zuoright.com/20220105231419.png)

## 主机模式

`docker run --network=host`

主机模式下，Docker 不会为新容器创建独立的网络名称空间，这样容器一切的网络设施，如网卡、网络栈等都直接使用宿主机上的真实设施，容器也就不会拥有自己独立的 IP 地址。此模式下与外界通信无须进行 NAT 转换，没有性能损耗，但缺点也十分明显，没有隔离就无法避免网络资源的冲突，譬如端口号就不允许重复。

## 空置模式

`docker run --network=none`

空置模式下，Docker 会给新容器创建独立的网络名称空间，但是不会创建任何虚拟的网络设备，此时容器能看到的只有一个回环设备（Loopback Device）而已。提供这种方式是为了方便用户去做自定义的网络配置，如自己增加网络设备、自己管理 IP 地址，等等。

## 容器模式

`docker run --network=container:指定共享网络的容器名称`

容器模式下，新创建的容器将会加入指定的容器的网络名称空间，共享一切的网络资源，但其他资源，如文件、PID 等默认仍然是隔离的。两个容器间可以直接使用回环地址（localhost）通信，端口号等网络资源不能有冲突。

问题：当某个容器网络出错，但不允许在这个容器内安装工具排查问题时要怎么办？

- 方法1：用container模式共享网络，在另一个容器里安装工具排查即可
- 方法2：只要能切换到目标容器命名空间上即可

该模式下的一些应用

![20220105233908](http://image.zuoright.com/20220105233908.png)

### MACVLAN 模式

`docker network create -d macvlan xxx`

此网络允许为容器指定一个副本网卡，容器通过副本网卡的 MAC 地址来使用宿主机上的物理设备，在追求通信性能的场合，这种网络是最好的选择。Docker 的 MACVLAN 只支持 Bridge 通信模式，因此在功能表现上与桥接模式相类似。

### Overlay 模式

`docker network create -d overlay xxx`

Docker 说的 Overlay 网络实际上就是特指 VXLAN，这种网络模式主要用于 Docker Swarm 服务之间进行通信。然而由于 Docker Swarm 败于 Kubernetes，并未成为主流，所以这种网络模式实际很少使用。
