# 引言

区别于自己家里的物理机，云厂商放置在专业机房的物理机被称为「物理服务器」，或叫独立服务器

由于云厂商的物理服务器配置很高，个人用户通常用不到这么高的配置，所以云厂商会将一台物理服务器宿主机分割成多个虚拟机，相当于我们自己使用 VMware 创建的虚拟机，被云厂商称之为 「VPS」(Virtual Private Server, 虚拟专用服务器)，每个 VPS 都拥有独立的 CPU、内存、存储、公网 IP 等操作系统资源，然后对外出售。

VPS 不支持调整资源，而 「ECS」(Elastic Compute Service 弹性计算服务) 支持根据需要随时弹性调整资源大小，当然，资费也是弹性的。

> 不同云厂商的产品名可能不一致
> 
> 有些云厂商还提供一种叫 「VM」（Virtual Machine 虚拟主机）的服务，资源共享，权限小，只允许安装特定软件

## 云厂商

大陆服务器对外提供网站服务需要备案，香港和境外的通常不需要

配置

- 1c, core 核(CPU)数
- 1g, Gigabyte 内存(RAM)大小

线路

- BGP 流量路由优化，多线接入
- CN2(China Telecom Next Generation Carrier Network) 中国电信提供的跨境网络服务
    - CN2 GIA(Global Internet Access) 性能更高更稳定
- Hinet 性能优化

国内

- 阿里云 ECS(Elastic Compute Service)
- 腾讯云 CVM(Cloud Virtual Machine)
- 莱卡云

国外

- AWS(Amazon Web Services)
    - EC2(Elastic Compute Cloud)
    - ECS(Elastic Container Service)
    - EKS(Elastic Kubernetes Service)
- Microsoft Azure
- Google Cloud
- BandwagonHost
- Vultr
- Linode
- Cloudflare 主要提供 CDN 和安全服务

## 软件虚拟化 QEMU

软件虚拟化，性能比较差，通常只用于模拟 I/O 设备

> <https://qemu.org>

QEMU 是开源的纯软件实现的虚拟化模拟器，几乎可以模拟任何硬件设备。

有两种主要工作模式

- 用户工作模式（仿真器）：可以在一种架构（如 x86 PC）下运行另一种架构（如 ARM）下的操作系统和程序，通过使用动态转换，它可以获得非常好的性能。
- 系统工作模式（虚拟机）：能模拟整个计算机系统，包括 CPU 及其他 IO 设备。它能运行和调试不同平台开发的操作系统，也能在宿主机上虚拟不同数量、不同平台的虚拟电脑。

与其他的虚拟化软件（如 VMware）不同，QEUM 不提供管理虚拟机的 GUI（运行虚拟机时出现的窗口除外），也不提供创建具有已保存设置的持久虚拟机的方法。因此，我们可以选择创建自定义脚本以启动虚拟机，这样就可以保存相应的参数，避免每次启动时手动指定所有运行参数。

```shell
sudo apt install qemu
```

## 硬件虚拟化 Hypervisor

虚拟机主要由三部分组成

- 客户机操作系统（Guest VM），逻辑操作系统
- 虚拟监视器（Hypervisor）：中间层，虚拟机最主要的部分
- 宿主机操作系统（Host）：物理操作系统

Hypervisor 通过硬件虚拟化功能，模拟出了运行一个操作系统需要的各种硬件，比如 CPU、内存、I/O 设备、磁盘、网卡等等。然后，它在这些虚拟的硬件上安装了一个新的操作系统，即 Guest OS。

虚拟化技术主要有

- KVM (Kernel-based Virtual Machine) 基于 Linux 内核的，需要硬件支持的完全虚拟化
- Hyper-V 微软提供的，集成在 Windows 系统中的虚拟化解决方案
- Xen 是一个独立的 hypervisor，支持完全虚拟化和半虚拟化
- OpenVZ 是一种基于容器的虚拟化技术，与 Docker 概念类似，但已经被淘汰，主要用于传统的 VPS 托管和遗留应用虚拟化

常见的虚拟机软件有

- Parallels 付费，通常用于 Windows 系统
- VMWare Workstation 付费（Player 为免费版，Fusion 适用于 Mac）
- VirtualBox 开源免费体积小
- UTM 免费（仅适用于 Mac）

虚拟机和 K8s 都很吃内存，所以建议宿主机内存 8G+（可以支持开两个虚拟机组成最小集群）4 核 CPU，硬盘 300G+
