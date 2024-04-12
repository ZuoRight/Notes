# Linux

Linux 是一个 Unix-like 系统，最初由 Linus Torvalds 基于 Unix 所编写

- [Unix timeline](https://commons.wikimedia.org/wiki/File:Unix_timeline.en.svg#/media/File:Unix_timeline.en.svg)
- [Unix history simple](https://zh.wikipedia.org/wiki/File:Unix_history-simple.svg)

![20220707143751](http://image.zuoright.com/20220707143751.png)

## 内核 Kernel

Linux 本身只是一个开源的内核(Kernel)

内核主要负责一些最基本的工作，比如：管理硬件驱动、管理内存、管理文件系统、管理进程等等。

内核是计算机资源的管理者，通过系统 API 函数向用户应用软件提供一些接口，会在处理器的特权模式（宏内核模式）下运行。

微内核架构正好与宏内核架构相反，它提倡内核功能尽可能少：仅仅只有进程调度、处理中断、内存空间映射、进程间通信等功能，这样的内核是不能完成什么实际功能，开发者们把实际的进程管理、内存管理、设备管理、文件管理等服务功能，做成一个个服务进程。和用户应用进程一样，只是它们很特殊，宏内核提供的功能，在微内核架构里由这些服务进程专门负责完成。微内核定义了一种良好的进程间通信的机制：消息。

`uname -a`

![20230401113035](http://image.zuoright.com/20230401113035.png)

内核版本：主版本号.次版本号.末版本号

> 次版本号，奇数为开发版，偶数为稳定版  
> <https://www.kernel.org/>

![20220707142531](http://image.zuoright.com/20220707142531.png)

## GNU工具集

- GCC/G++：编译环境
- glibc：C库
- coreutils：核心工具组

## 发行版

而我们平时常用的Linux其实指的是Linux发行版（distribution），它是在Kernel的基础上定制化扩展了很多服务和功能。

发行版本千千万，不是红帽就大便，意思就是Linux的发行版有很多，但主要分两个派系：Redhat 和 Debian

> 发行版列表：<https://zh.wikipedia.org/wiki/Linux%E5%8F%91%E8%A1%8C%E7%89%88>

![20220707151705](http://image.zuoright.com/20220707151705.png)

> 发行版选择器：<https://distrochooser.de/>

比较常见的是

- Ubuntu
- CentOS
- Alpine 容器中可能比较常用

```shell
# 查看发行版信息
lsb_release -a
<<"COMMENT"
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 18.04.6 LTS
Release:        18.04
Codename:       bionic
COMMENT
```

## Ubuntu

> <https://ubuntu.com/download>

Ubuntu LTS(长期支持)版本每两年发布一次，一般能提供5年的支持。

- Ubuntu 18.04.6 LTS (Bionic Beaver) 内置Py3.6
- Ubuntu 20.04.5 LTS (Focal Fossa) 内置Py3.8
- Ubuntu 22.04.1 LTS (Jammy Jellyfish) 内置Py3.10

### 包管理器

> 包管理器：`apt`（等价于`apt-get`+`apt-cache`+`apt-config`最常用命令集合）
>
> 安装包格式：`.deb`

- 网络安装

![20220712160747](http://image.zuoright.com/20220712160747.png)

```shell
apt update
apt list --installed  # 已安装
apt list --upgradable  # 可升级
apt upgrade  # 升级所有可升级的软件包

apt install xxx
"""
-y：yes，在命令行交互提示中，直接输入 yes
-f：fix解决包依赖的问题
--no-install-recommends 参数来避免安装非必须的文件，从而减小镜像的体积

- 下载的软件的存放位置：/var/cache/apt/archives
- 安装后软件的默认位置：/usr/share
- 可执行文件位置：/usr/bin
- 配置文件位置：/etc
- lib文件位置：/usr/lib
"""
```

- 本地安装

```shell
ls -l /dev/sr0  # 查看块设备
mount /dev/sr0 /mnt  # 建议挂载到空目录/mnt

dpkg -i /mnt/name-xxx.deb  # 安装
dpkg -P name  # 卸载
```

### 源码编译安装

```shell
wget https://xxx.tar.gz  # 下载tarball(压缩包)文件
sudo tar xfvz xxx.tar.gz -C  /usr/local/src  # 解压缩
cd xxx/  # 可以查看安装说明：README或INSTALL

./configure --prefix=/usr/local/openresty  # 自动生成makefile文件
"""
默认会安装到usr/local，安装完不需要配置环境变量
建议安装到指定目录（方便卸载，直接删除目录），需要配置环境变量
"""

make clean  # 清除旧的目标文件
make [-j2]  # 编译，使用2个逻辑CPU
make check  # 检查
make install  # 将编译完成的文件安装到预定的目录中

# 更新
"""
下载patc文件，更新源代码
更新源代码后，需要重新编译：make
"""

# 删除
"""
手动删除，rm -rf 软件目录
"""
```

### wget 下载

> Linux下一种类似与迅雷的下载软件，通过HTTP、HTTPS、FTP三个最常见的TCP/IP协议下载

```shell
wget -c xxx  # -c 支持断点续传
```

### PPA

Ubuntu 提供了一个名为 Launchpad 的平台，使软件开发人员能够创建自己的软件仓库，即 Personal Package Archive（个人软件包存档），终端用户可以将 PPA 仓库添加到 sources.list 文件中，然后便可以使用 apt 来安装

```shell
sudo add-apt-repository ppa:demo/xxx
sudo apt update
sudo apt install xxx
```

### 常用工具

```shell
apt install busybox -y  # 精简工具集

apt install -y git vim curl jq

# 安装openssh-server用于远程登录（Ubuntu桌面版默认不支持）
apt install -y openssh-server
```

## CentOS

> 包管理器：`yum`（Yellow dog Updater, Modified）
>
> 安装包格式：`name-软件版本-系统版本.平台.rpm`

- 网络安装

> 配置文件：`/etc/yum.repos.d/CentOS-Base.repo`

```shell
yum search/list/install/remove/update xxx
```

- 本地安装

官网找`·.rpm`安装包，然后挂载到空目录

```shell
ls -l /dev/sr0  # 查看块设备
mount /dev/sr0 /mnt  # 建议挂载到空目录/mnt

rpm -i /mnt/name-xxx.rpm  # 安装install
rpm -qa | more  # 查询所有已安装的软件包
rpm -qi name  # 查询指定安装包信息
rpm -e name  # 卸载
```

```shell
# 升级内核
apt/yum install kernel-xxx

# 更新已安装的其它软件包和补丁
apt/yum update

# 用户登录
su - username

reboot  # 重启
shutdown  # 关机
```
