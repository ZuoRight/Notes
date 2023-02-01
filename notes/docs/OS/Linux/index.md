# 引言

Linux最初由Linus Torvalds基于Unix所编写，也就是说它是一个Unix-like系统，从[Unix timeline](https://commons.wikimedia.org/wiki/File:Unix_timeline.en.svg#/media/File:Unix_timeline.en.svg)或者[Unix history simple](https://zh.wikipedia.org/wiki/File:Unix_history-simple.svg)可以大概了解到它和Unix系统的一个关系

![20220707143751](http://image.zuoright.com/20220707143751.png)

## 内核

Linux本身只是一个开源的内核(Kernel)

> Kernel版本：<https://www.kernel.org/>

版本号：主版本号.次版本号(奇/偶数为开发/稳定版).末版本号

![20220707142531](http://image.zuoright.com/20220707142531.png)

```sh
# 查看内核信息
uname
"""
-s 查看系统内核(默认) Linux
-r 查看系统内核版本 5.4.0-26-generic
-m 查看系统硬件架构 x86_64
-a 查看所有信息

Linux localhost.localdomain 5.4.0-26-generic #30-Ubuntu SMP Mon Apr 20 16:58:30 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
"""

# 升级内核
yum/apt install kernel-xxx

# 更新已安装的其它软件包和补丁
yum/apt update
```

## 发行版

而我们平时常用的Linux其实指的是Linux发行版，它是在Kernel的基础上定制化扩展了很多服务和功能

俗话说，发行版本千千万，不是红帽就大便，意思就是Linux的发行版有很多，但主要分两个派系：Redhat和Debian

> 发行版列表：<https://zh.wikipedia.org/wiki/Linux%E5%8F%91%E8%A1%8C%E7%89%88>

![20220707151705](http://image.zuoright.com/20220707151705.png)

> 发行版选择器：<https://distrochooser.de/>

尽管有这么多的发行版，但我们平时可能使用较多的还是Ubuntu和CentOS，以及在容器中可能比较常用的Alpine

### Ubuntu

> <https://ubuntu.com/download>

Ubuntu LTS(长期支持)版本每两年发布一次，一般能提供5年的支持。

- 20.04(Focal Fossa)
- 22.04(Jammy Jellyfish) 推荐

## GNU工具集

- GCC/G++：编译环境
- glibc：C库
- coreutils：核心工具组

## 启动过程

1. `BIOS` 基本的输入输出系统，在主板上，在启动时按F2进入界面，选择引导介质
2. `MBR` 主引导记录部分，在硬盘上
3. `BootLoader` grub2.0 用于启动和引导内核的工具，即选择内核和指定版本
4. `Kernel` 内核
5. `systemd` 1号进程(以前是`init`)
6. `系统初始化`
7. `Shell` 用于解释用户对操作系统的操作

```bash
reboot  # 重启
shutdown  # 关机
```

## 包管理器

### RedHat/CentOS

> 包管理器：`yum`（Yellow dog Updater, Modified）
>
> 安装包格式：`name-软件版本-系统版本.平台.rpm`

- 网络安装

> 配置文件：`/etc/yum.repos.d/CentOS-Base.repo`

```bash
yum search/list/install/remove/update xxx
```

- 本地安装

官网找`·.rpm`安装包，然后挂载到空目录

```bash
ls -l /dev/sr0  # 查看块设备
mount /dev/sr0 /mnt  # 建议挂载到空目录/mnt

rpm -i /mnt/name-xxx.rpm  # 安装install
rpm -qa | more  # 查询所有已安装的软件包
rpm -qi name  # 查询指定安装包信息
rpm -e name  # 卸载
```

### Debian/Ubuntu包管理器

> 包管理器：`apt`（等价于`apt-get`+`apt-cache`+`apt-config`最常用命令集合）
>
> 安装包格式：`.deb`

- 网络安装

![20220712160747](http://image.zuoright.com/20220712160747.png)

```bash
apt list --installed
apt update

# 安装常用工具
apt install -y git vim curl jq
# 安装openssh-server用于远程登录（Ubuntu桌面版默认不支持）
apt install -y openssh-server
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

```bash
ls -l /dev/sr0  # 查看块设备
mount /dev/sr0 /mnt  # 建议挂载到空目录/mnt

dpkg -i /mnt/name-xxx.deb  # 安装
dpkg -P name  # 卸载
```

### 源码编译安装

```bash
wget https://xxx.tar.gz  # 下载tarball(压缩包)文件
tar xfvz xxx.tar.gz -C  /usr/local/src  # 解压缩
cd xxx/  # 可以查看安装说明：README或INSTALL

./configure --prefix=/usr/local/openresty  # 自动生成makefile文件
"""
默认会安装到usr/local，安装完不需要配置环境变量
建议安装到指定目录（方便卸载，直接删除目录），需要配置环境变量：export PATH=xxx
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

## 存储

- 块存储

最古老的数据存储形式，性能好，吞吐量高，延迟低。数据都储存在固定长度的一个或多个块（Block）中，想要读写访问数据，就必须使用与存储相匹配的协议（SCSI、SATA、SAS、FCP、FCoE、iSCSI……）来进行。

- 文件存储

便于人类使用和管理，POSIX事实标准，绝大多数传统的文件存储都是基于块存储之上去实现的。

人们把定义文件分配表（FAT，专门组织块结构来构成文件的分配表）应该如何实现、储存哪些信息、提供什么功能的标准称为文件系统（File System），FAT32、NTFS、exFAT、ext2/3/4、XFS、BTRFS 等都是很常用的文件系统。

- 对象存储

是一种随着云数据中心的兴起而发展起来的存储，天生的分布式特性，以及极其低廉的扩展成本，使它很适合于CDN一类的应用，拿来存放图片、音视频等媒体内容，以及网页、脚本等静态资源。

## 文件系统

- ext4
- xfs
- NTFS