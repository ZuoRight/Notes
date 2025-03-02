# 引言

[参考手册](https://docs.docker.com/reference/){ .md-button .md-button--primary }

从过去以物理机和虚拟机为主体的开发运维环境，向以容器为核心的基础设施转变的过程，涵盖了对网络、存储、调度、操作系统、分布式原理等各个方面的容器化理解和改造。

最初大家就是租一批虚拟机，然后像管理物理服务器那样，用脚本或者手工的方式在这些机器上部署应用。但部署过程会碰到云端和本地环境不一致的问题，所以需要一套应用打包功能，而 dotCloud 公司开源的 Docker 使用镜像解决了这个问题，直接打包了应用以及运行所需要的整个环境，从而保证了本地和云端环境的强一致性，对其他 PaaS 公司实施了降维打击。

但一整套服务涉及很多的容器，所以随之而来的另一个问题就是容器的编排，于是爆发了一场关于容器编排的战争。Kubernetes(K8s) 凭借 Google 和 CNCF 社区的强力背书，击败了 Docker Swarm 和 Apache Mesos 等对手，成为云原生操作系统的事实标准，而 Docker Engine 这样一个只能用来创建和启停容器的小工具，最终只能充当这些平台项目的幕后英雄。

![20240626192601](https://image.zuoright.com/20240626192601.png)

虚拟机使用了 Hypervisor 硬件虚拟化技术，虚拟出包括操作系统在内的整个运行环境，比较消耗资源

而容器利用了 Linux 内核提供的 NameSpace 和 Cgroup 技术，仅仅是隔离出一个独立的进程，与宿主机共享内核，性能更好

凡是挑内核的场景都不应使用 Docker，比如不能做系统兼容性测试，也不能在 Docker 环境中驱动 IE 浏览器。

另外计算密集型业务，也不适合使用容器。

```shell
docker  # 查看所有命令选项
docker <command> --help  # 查看命令使用帮助
docker --version  # 查看版本
docker version  # 详细版本信息
docker info  # 系统信息
'
Server: 
  Containers: 1 
    Running: 0 
    Paused: 0 
    Stopped: 1 
  Images: 8 
  Server Version: 20.10.12 
  Storage Driver: overlay2  # 存储用的文件系统
    Backing Filesystem: extfs 
  Cgroup Driver: systemd 
  Default Runtime: runc 
  Kernel Version: 5.13.0-19-generic  # Linux内核
  Operating System: Ubuntu Jammy Jellyfish (development branch)  # 操作系统
  OSType: linux 
  Architecture: aarch64  # 硬件
  CPUs: 2 
  Total Memory: 3.822GiB  # 内存
  Docker Root Dir: /var/lib/docker
'

# 测试
docker run hello-world
docker run -p 80:80 docker/getting-started
```

## Docker Desktop

由于容器没有虚拟化内核，而是与宿主机共用内核，所以 Docker 必须部署在 Linux 内核的系统上

容器中安装的各种操作系统镜像其实只是套了壳的软件而已，只包含操作系统的用户空间。

`Docker Desktop for Windows/Mac` 本质上是基于 Hyper-V 等虚拟机安装的，类似于在 VMWare 或 VirtualBox 中安装。

属于商业化产品，有一些非通用的东西。

### 配置

以 Mac 为例：<https://docs.docker.com/desktop/settings/mac/>

- Resources Proxies

![20240728185230](https://image.zuoright.com/20240728185230.png)

- Advance

Docker CLI 如果安装在 System，则会自动映射到 `/usr/local/bin`

如果安装在 User，则需要手动配置环境变量

```shell
export PATH=$PATH:/Applications/Docker.app/Contents/Resources/bin
```

- 镜像加速

> 如果配置了 Resources Proxies，则不需要再用镜像

参考：[阿里云镜像加速器](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

![20210726135953](https://image.zuoright.com/20210726135953.png)

### OrbStack

是一种在 macOS 上替代 Docker Desktop 和 虚拟机运行容器和 Linux 的方法，快速、轻量且简单

还包含一个针对开发进行了优化的轻量级单节点 Kubernetes 集群，并配有 GUI 和网络集成。

直接官网下载：<https://orbstack.dev/>，或者 `brew install orbstack`

- 设置代理

<https://docs.orbstack.dev/docker/network#proxies>

```shell
# HTTP
orb config set network_proxy http://example.com
# HTTPS with password on port 8443
orb config set network_proxy https://user:password@example.com:8443
# SOCKS with password on port 1081
orb config set network_proxy socks5://user:password@example.com:1081

# reset to system proxy
orb config set network_proxy auto
# disable proxy
orb config set network_proxy none
```

## Docker Engine

<https://docs.docker.com/engine/install/>

仅适用于 Linux 的 Docker Engine 是完全免费开源的

```shell
# Ubuntu
# https://docs.docker.com/engine/install/ubuntu/

# 移除各种旧版本
sudo apt-get remove docker docker-engine docker.io containerd runc

# 设置存储库
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 安装
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

```shell
# ubuntu安装完会自启动
sudo systemctl start docker
# 开机自启动
sudo systemctl enable docker

# 操作Docker必须要有root权限
# 推荐将当前用户加入docker组（需要exit重新登录后生效）
sudo usermod -aG docker ${USER}
```

### 拉取镜像使用 mirror 加速

> <https://docs.docker.com/config/daemon/>

```shell
cd /etc/docker
vim daemon.json  # 添加 registry-mirrors 配置，比如阿里云
sudo systemctl daemon-reload  
sudo systemctl restart docker  
```

### 拉取镜像时使用代理

> <https://docs.docker.com/config/daemon/systemd/>

创建 `proxy.conf` 文件

```shell
sudo mkdir -p /etc/systemd/system/docker.service.d
sudo vim /etc/systemd/system/docker.service.d/proxy.conf
```

设置代理

```yaml
[Service]
Environment="ALL_PROXY=socks5://127.0.0.1:1081"
```

重启生效

```shell
sudo systemctl daemon-reload  # 重新加载配置文件
sudo systemctl restart docker  # 重启docker
sudo systemctl show --property=Environment docker  # 验证
```

### 构建镜像使用代理

- 方式1

`docker build --build-arg all_proxy="socks5://127.0.0.1:1081"`

- 方式2

> <https://docs.docker.com/engine/reference/commandline/cli/#configuration-files>

`~/.docker/config.json`

```json
{
    "proxies": {
        "default": {
            "allProxy": "socks5:://127.0.0.1:1081"
        }
    }
}
```

### 容器中使用代理

- 方式1

1. 查看 Docker 在宿主机的虚拟网卡 `docker0` 的IP：`ip addr show docker0`，通常为：`172.0.0.1`
2. 容器内设置全局代理到 docker0：`export all_proxy="socks5://172.0.0.1:1081"`

> 注意：在 Windows 和 macOS 平台下并没有 docker0 虚拟网卡，这时候可以使用 `host.docker.internal` 这个特殊的 DNS 名称来解析宿主机 IP

- 方式2（推荐）

1. 容器网络设置为：`--network host` 或者 `-p 1081:1081`
2. 容器内设置全局代理

```shell
export all_proxy="socks5://127.0.0.1:1081"
alias proal1='export http_proxy=http://127.0.0.1:8123 https_proxy=http://127.0.0.1:8123'
alias proal0='unset http_proxy https_proxy'
```

## 原理架构

Docker 使用可 C/S 架构，docker-cli 传输命令给 docker daemon 执行

![20240626193948](https://image.zuoright.com/20240626193948.png)

操作系统分为用户空间和内核空间，应用程序运行在用户空间。Docker 将程序和环境（`用户空间 + 文件系统 + 依赖库` 等）一起打包压缩成「基础镜像」。然后将 `基础镜像 + 程序 + DockerFile` 打包压缩成「容器镜像」

![20220704225726](https://image.zuoright.com/20220704225726.png)

- Images 镜像：包含运行软件所需的一切要素（代码和运行时）
- Container 容器：镜像的实例
- Registry 镜像仓库：管理镜像的仓库，类似代码库 GitHub
- Docker Compose：使用 YAML 文件对多个容器进行编排，即一整套服务的部署
- Docker Swarm：调度一整套服务在多台机器上的集群部署

![20210910000945](https://image.zuoright.com/20210910000945.png)

- 容器进程，运行在由 Linux Namespace 和 Cgroups 构成的隔离环境里
- 可读写层，它以 Copy-on-Write 的方式存放任何对只读层的修改，容器声明的 Volume 挂载点就在这层。
- Init 层，Docker 自己添加的，用来存放被临时修改过的 `/etc/hosts` 等文件。
- 只读层

镜像使用了分层的设计，基础镜像可以被多个容器镜像共享

结合使用 Mount Namespace 和 rootfs，容器就能够为进程构建出一个完善的文件系统隔离环境。当然，这个功能的实现还必须感谢 chroot 和 pivot_root 这两个系统调用切换进程根目录的能力。而在 rootfs 的基础上，Docker 公司创新性地提出了使用多个增量 rootfs 联合挂载一个完整 rootfs 的方案，这就是容器镜像中「层」的概念。

Docker 创建容器初始化进程，负责完成根目录的准备、挂载设备和目录、配置 hostname 等一系列需要在容器内进行的初始化操作。最后，通过 `execv()` 系统调用，让应用进程取代自己，成为容器里的 `PID=1` 的进程。

### NameSpace

命名空间有很多种，分别用于隔离不同的东西

> 查看内核支持的 Namespace：`ls -l /proc/self/s`

- PID Namespace
- Mount Namespace
- Network Namespace

```shell
# 容器在宿主机上是一个真实存在的进程
docker inspect --format '{{ .State.Pid }}' <容器ID>
# 查看容器所有 Namespace 对应的文件
ls -l /proc/<容器pid>/ns

# 指定–net=host，就意味着这个容器不会为进程启用 Network Namespace。这就意味着，这个容器拆除了 Network Namespace 的“隔离墙”，所以，它会和宿主机上的其他普通进程一样，直接共享宿主机的网络栈。这就为容器直接操作和使用宿主机网络提供了一个渠道。
docker run -it --network container:4ddf4638572d busybox ifconfig
```

### Cgroup

用于限制资源的使用

Linux Control Group 最初由 Google 实现，被用于 Borg 系统（K8s的前身），后被集成进 Linux 内核。

它最主要的作用，就是限制一个进程组能够使用的资源上限，包括 CPU、内存、磁盘、网络带宽等等。此外，Cgroup 还能够对进程进行优先级设置、审计，以及将进程挂起和恢复等操作。

对于 Linux 系统而言，容器就是一组进程的集合。如果容器中的应用创建过多的进程或者出现 bug，就会产生类似 `fork bomb` 的行为。

> fork bomb 指在计算机中，通过不断建立新进程来消耗系统中的进程资源，它是一种黑客攻击方式。这样，容器中的进程数就会把整个节点的可用进程总数给消耗完。

这样，不但会使同一个节点上的其他容器无法工作，还会让宿主机本身也无法工作。所以对于每个容器来说，我们都需要限制它的最大进程数目，而这个功能由 pids Cgroup 这个子系统来完成。

pids Cgroup 通过 Cgroup 文件系统的方式向用户提供操作接口，在一个容器建立之后，创建容器的服务会在挂载点 `/sys/fs/cgroup/pids` 下建立一个子目录，就是一个控制组，控制组里最关键的一个文件就是 `pids.max`。我们可以向这个文件写入数值（即容器中允许的最大进程数目）。

容器是一个单进程模型，在一个容器中，你没办法同时运行两个不同的应用，跟 NameSpace 的情况类似，Cgroup 对资源的限制能力也有很多不完善的地方，被提及最多的自然是 `/proc` 文件系统的问题。

如果在容器里执行 top 指令，就会发现，它显示的信息居然是宿主机的 CPU 和内存数据，而不是当前容器的数据。造成这个问题的原因就是，`/proc` 文件系统并不知道用户通过 Cgroup 给这个容器做了什么样的资源限制，即 `/proc` 文件系统不了解 Cgroup 限制的存在。
