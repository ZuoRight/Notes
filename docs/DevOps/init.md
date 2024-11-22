# 系统初始化

![20240129135516](https://image.zuoright.com/20240129135516.png)

主板加电，CPU 进入实模式，执行 ROM 中的 BIOS 或 UEFI 启动固件

启动固件检测正常后，搜寻存储器找到启动盘，加载 MBR 或 ESP 到内存，启动引导加载程序，加载硬盘分区中的 OS 文件

在内核完成了操作系统的各种初始化之后，会从缺省路径尝试启动第一个用户态进程，即1号进程，也叫初始化守护进程，然后操作系统便从内核态切换到了用户态。

## 启动固件

启动固件(Fireware)固化在主板 ROM 芯片中，主要有早期的 BIOS 和最新的 UEFI 两种

### BIOS

Basic I/O System，基本输入输出系统

![20221124223743](http://image.zuoright.com/20221124223743.png)

开机时，BIOS会做开机自检(P.O.S.T)，检测硬件正常后，按照CMOS设置里的顺序搜索引导设备（大概率是硬盘），挨个查看存储设备的前512字节是不是以0x55 0xAA结尾，如果不是，那就跳过找下一个设备；如果是的话，则表示这个磁盘可以启动，然后交给引导程序（Bootloader，比如GRUB2）

> 开机自检（Power On Self Test）会给出提示音，比如哔一声表示正常，长哔三声表示键盘错误，连续短哔可能RAM有问题

![20221124232639](http://image.zuoright.com/20221124232639.png)

CMOS最初是主板上一个单独的芯片，后来被集成到了南侨芯片组，它属于一种RAM，用于存储BIOS的配置（计算机的启动顺序、安装的磁盘驱动器类型、系统时钟的当前日期和时间等），所以也叫RTC(实时时钟)或NVRAM(非易失性RAM)，为避免计算机断电后恢复为默认值，所以CMOS有单独的纽扣电池提供电源（可以充电长达十余年）

![20221124224838](http://image.zuoright.com/20221124224838.png)

### UEFI

Unified Extensible Firmware Interface 统一可扩展固件接口

![20221124225800](http://image.zuoright.com/20221124225800.png)

UEFI不仅仅是BIOS的替换，本质上是一个运行在PC固件之上的微型操作系统，支持更友好的界面，还可以使用鼠标。

BIOS只能识别固定位置的磁盘引导块，而UEFI可以存储在主板的闪存中，也可以在启动时从硬盘或网络共享加载。

UEFI采用GPT分区表的方式后，硬盘容量和分区数目几乎没有上限（目前windows支持最大128个分区）

使用了安全引导，以防止加载未数字签名的驱动程序。在引导过程中，UEFI固件仅在实模式下初始化平台，平台初始化后，UEFI使用基本操作系统为后续启动启用64位保护模式。允许在启动过程中进行远程监视和控制，可防止恶意访问。

## Bootloader

Bootloader 引导加载程序，位于 MBR 或 ESP 中， 用于从多操作系统的计算机中选择一个系统来启动，或从系统分区中选择特殊的内核配置，是硬件和操作系统之间的接口

![20221124222816](http://image.zuoright.com/20221124222816.png)

Linux 发行版中可使用的 Bootloader 有三种

- `GRUB` GRand Unified Bootloader
- `GRUB2` 最新且最常用
- `LILO`

GRUB2，包含 `boot.img`(启动代码) 和 `core.img`(内核镜像)

`core.img` 主要包括以下模块

- `diskroot.img`
- `lzma_decompress.img` 解压缩程序
- `kernel.img`

## 加载顺序

1. `boot.img` 加载 `core.img` 的 `diskroot.img`
2. `diskroot.img` 加载 `lzma_compress.img`
3. CPU切换到保护模式，启用分段（辅助进程管理），启动分页（辅助内存管理），打开其他地址线
4. `lzma_compress.img` 解压运行 `kernel.img`，解析 `/boot/grub2/grub.cfg`(GRUB2配置文件)，选择操作系统，检查通过后加载完整系统内核

## 初始化配置文件

当操作系统启动时会加载一系列的初始化配置文件，这些文件控制着系统的各种设置和服务

```shell
# 系统级配置文件
/etc/environment
/etc/profile  # 用于设置全局的环境变量和默认的环境参数等
/etc/bashrc  # bash shell 的配置文件，其它shell不受影响
/etc/inittab
/etc/fstab  # 定义了系统中各个文件系统的挂载点和选项
/etc/passwd  # 用户
/etc/group  # 用户组
/etc/hosts  # 让主机名（域名）映射到指定的IP地址
/etc/resolv.conf  # 定义了系统使用的DNS服务器地址和搜索域名
```

比如正在开发一个网站，域名为 `newwebsite.com`，在还没有正式指向线上服务器之前，在开发环境下可使用 `/etc/hosts` 来将域名直接映射到指定 IP 去测试。

```shell
# 用户级配置文件
~/.bashrc  # 每启动shell时都会加载一次，主要用于定义环境变量、命令别名等

~/.bash_profile  # 仅登录时执行一次，如果存在则后面的不再执行，主要用于定义个人偏好
~/.bash_login  # 上面的不存在才会执行
~/.profile  # 上面的不存在才会执行

~/.bash_logout  # 每次退出时执行
```

加载顺序：实线为主流程，虚线为被调用的配置文件

![e4da92fd9a72e844053abca2d931f2d](http://image.zuoright.com/e4da92fd9a72e844053abca2d931f2d.jpg)

优先级（就近原则）：用户级配置文件 > 应用程序配置文件 > 系统级配置文件

## 初始化守护进程

每个子进程都是由父进程启动的，1号进程便是初始化进程，它最基本的功能就是创建出 Linux 系统中其他所有的进程，并且管理这些进程

初始进程在旧版本系统中是 `init`，新版本通常是 `systemd`

```shell
# 查看进程树
#   -u 显示所属用户
#   -p 显示进程编号（PID）
pstree [-up]
```

![20210830230858](http://image.zuoright.com/20210830230858.png)

### init

`SysV init`, SysV 是 `System V` 的缩写

```shell
# 对应的管理工具：service
service network start/stop/restart xxx

chkconfig -list network
```

### Systemd

Systemd 是最新的守护进程系统，会尽可能启动较少的进程，尽可能并发启动更多进程

目前主流的 Linux 发行版都会把 `/sbin/init` 作为符号链接指向 `Systemd`

```shell
ls -l /sbin/init  # lrwxrwxrwx 1 root root 20 Jul 21 19:00 /sbin/init -> /lib/systemd/systemd
```

![20230204012419](http://image.zuoright.com/20230204012419.png)

```shell
# 对应的管理工具：systemctl
# 负责检查和控制 systemd 系统和服务管理器
systemctl status xxx
systemctl start/stop/restart/reload xxx

systemctl enable/disable xxx  # 开机自启/关闭

systemctl list-unit-files xxx.service
```
