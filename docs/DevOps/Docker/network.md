# 网络

即容器间如何通信

安装 Docker 时会自动在宿主机上创建一个名为 `docker0` 的网桥，本质上是一个虚拟交换机

- 虚拟路由器：Linux 内核
- 虚拟交换机：Linux Bridge，docker0
- 虚拟网卡：成对出现，比如 veth

> 交换机是根据 MAC 地址做二层转发的，不允许给接入的设备设置 IP 地址

网桥与物理交换机不同的是，允许给自己设置 IP 地址。如果数据包的目的 MAC 地址为网桥本身，并且网桥有设置了 IP 地址的话，那该数据包即被认为是收到发往创建网桥那台主机的数据包，此数据包将不会转发到任何设备，而是直接交给上层（三层）协议栈去处理

## 网络模式

```shell
# 查看有哪些网络模式
docker network ls
'
NETWORK ID     NAME      DRIVER    SCOPE
e01de21fde14   bridge    bridge    local
28abc4c947b8   host      host      local
d58fdf42ccb2   none      null      local
'
```

默认有三个 `SCOPE=local` 的网络模式，NAME 默认同 DRIVER，其他的网络模式可以通过不同的方式创建添加

- null 禁用网络
- host 主机模式
- bridge 桥接模式
- container
- macvlan
- overlay

host 简单粗暴效率高，适合小规模集群的简单拓扑结构

bridge 适合大规模集群，有了 bridge 就有更多的可操作空间，比如 XLAN 和 VXLAN 这些，它可以提供更多的可定制化服务，比如流量控制、灰度策略这些，从而像 flannel 和 Calico 这些组件才有了更多的发挥余地。

### 创建网络

系统默认会有一个叫 bridge 的网络，从名字就可以看出来是 bridge 模式。

但如果我们在生产环境中想要使用网络时，建议自己创建一个新的网络，而不要用默认的这个名为 bridge 的网络

```shell
# 创建网络，--driver/-d 指定网络模式，默认bridge模式
# 注意，这里是创建网络，不是创建容器，-d也不是后台运行的意思
docker network create [--driver bridge] my-net
docker network rm my-net  # 删除网络
```

### 使用网络

```shell
# 运行或创建容器时指定网络，如果不指定则默认连接到bridge网络
# -net / --network
docker create/run --name=demo --network net-name -d myapp

# 上面只能指定一个网络，可以用connect连接更多的网络
docker network connect my-net my-nginx
docker network disconnect my-net my-nginx # 断开网络

# 查看某个网络连接了哪些容器以及IP等信息
docker network inspect net-name
```

### 端口映射

容器默认不会对外发布网络，需要使用`-p` 或 `--publish` 参数将端口映射到宿主机，供docker外部服务或未连接到此容器的其它容器使用，也可以-P随机指定端口映射

host 网络模式下会忽略 -p 选项

```shell
docker run --name test -p <ip>:4000:80 <镜像名>:tag

docker port <容器ID>  # 查看容器端口映射
"""
80/tcp -> 0.0.0.0:8089
80/tcp -> :::8089
"""
```

## bridge 模式

> 参考：<https://docs.docker.com/network/network-tutorial-standalone/#use-the-default-bridge-network>

`docker run --network bridge`

桥接模式下，Docker 会为新容器分配独立的网络名称空间，为每个容器自动分配好 IP 地址（范围是 `172.17.0.0/24`），并且设置所有容器的网关均为 docker0（地址默认是 `172.17.0.1`），创建好 veth pair，一端接入容器，另一端接入到 docker0 网桥上，这样所有接入同一个网桥内的容器直接依靠二层网络来通信。

![20220105231419](http://image.zuoright.com/20220105231419.png)

- 示例1

```shell
'''
假设有app-1、app-2、app-3三个容器
1和2连接默认的网络，即 bridge，2和3连接到自定义的 net-demo
'''
docker run -dit --name app-1 alpine ash
docker run -dit --name app-2 --network net-demo alpine ash
docker run -dit --name app-3 --network net-demo alpine ash
docker network connect bridge app-2

# 分别检查bridge和net-demo网络信息
docker network inspect bridge
docker network inspect net-demo

# 进入运行在后台的容器1
docker attach app-1
ip addr show  # 查看容器的IP

# 进入运行在后台容器2
docker attach app-1
ping app-3  # 测试能否用容器名连接到同net-demo网络的容器3，可以
ping app-1  # 测试能否用容器名连接到同bridge网络的容器1，失败
ping ip-1  # 使用容器1的ip连接，成功，也就是说使用默认的bridge网络时，容器间只能用ip互联
```

- 示例2

```shell
'''
myapp1与mysql1连接到同一个自建网络my-custom-net
然后就可以在myapp1指定--host=mysql1来使用mysql1提供的数据库服务
'''
docker run --name=mysql1 --network=my-custom-net -d mysql/mysql-server
docker run --name=myapp1 --network=my-custom-net -d myapp
docker exec -it myapp1 mysql --host=mysql1 --user=myuser --password
```

## host 模式

> 参考：<https://docs.docker.com/network/network-tutorial-host/>

`docker run --network host`

主机模式下，Docker 不会为新容器创建独立的网络名称空间，这样容器一切的网络设施，如网卡、网络栈等都直接使用宿主机上的真实设施，容器也就不会拥有自己独立的 IP 地址。

此模式下与外界通信无须进行 NAT 转换，没有性能损耗，但缺点也十分明显，没有隔离就无法避免网络资源的冲突，譬如端口号就不允许重复。

## null 禁用网络

> 参考：<https://docs.docker.com/network/none/>

`docker run --network none`

空置模式下，Docker 会给新容器创建独立的网络名称空间，但是不会创建任何虚拟的网络设备，此时容器能看到的只有一个回环设备（Loopback Device）而已。提供这种方式是为了方便用户去做自定义的网络配置，如自己增加网络设备、自己管理 IP 地址，等等。

## 其它网络模式

### MACvlan

> 参考：<https://docs.docker.com/network/network-tutorial-macvlan/>

`docker network create -d macvlan xxx`

此网络允许为容器指定一个副本网卡，容器通过副本网卡的 MAC 地址来使用宿主机上的物理设备，在追求通信性能的场合，这种网络是最好的选择。

Docker 的 MACVLAN 只支持 Bridge 通信模式，因此在功能表现上与桥接模式相类似。

只有linux3.9+支持此种模式

### IPvlan

> 参考：<https://docs.docker.com/network/ipvlan/>

### Overlay

> 参考：<https://docs.docker.com/network/network-tutorial-overlay/>

`docker network create -d overlay xxx`

Docker 说的 Overlay 网络实际上就是特指 VXLAN，这种网络模式主要用于 Docker Swarm 服务之间进行通信。然而由于 Docker Swarm 败于 Kubernetes，并未成为主流，所以这种网络模式实际很少使用。
