---
comments: true
hide:
  - feedback
---

# 引言

DevOps(Development and Operations) 是一种文化、运动或实践，提倡尽可能地自动化软件开发和部署流程。

- CI(Continuous Integrationv) 持续集成
- CD(Continuous Deploymen) 持续交付

![20240522103905](https://image.zuoright.com/20240522103905.png)

![20210808174712](http://image.zuoright.com/20210808174712.png)

DevOps 依赖一系列工具来实现其目标，包括版本控制系统、自动化部署工具、配置管理工具、监控服务等

## VCS

Version Control System 版本控制系统

用于跟踪文本文件的改动，对于二进制文件只能跟踪其大小的变化

- 文本文件：各种源代码、纯文本等
- 二进制文件(Blob, binary large object)：图片、视频、office文件（其实是个压缩包，由很多.xml和其他文件构成，所以属于二进制）

VCS 主要分为

- 本地版本控制系统，比如：RCS
- 集中式版本控制系统，比如：SVN
- 分布式版本控制系统，比如：Git

## 代码托管平台

- GitHub：现已被微软收购，最大的托管平台
- GitLab：乌克兰企业，在中国创建了合资品牌（极狐），主要以开源版本著称，组织可以私有化部署，当然也提供云托管服务
- Bitbucket：Jira母公司Atlassian旗下产品
- Gitee(码云)：开源中国和工信部联合推出
- Coding：并购了Gitcafe，后又被腾讯云全资收购

## 云服务

![20240626225531](https://image.zuoright.com/20240626225531.png)

- SaaS(Software-as-a-service) 软件服务，用户拿来即用
- PaaS(Platform-as-a-service) 平台服务，提供软件部署平台，用户只需要关注业务逻辑，不需要关注底层
- IaaS(Infrastructure-as-a-service) 基础设施服务，主要提供一些基础资源，用户需要自己控制底层，实现基础设施的使用逻辑

## 容器

从过去以物理机和虚拟机为主体的开发运维环境，向以容器为核心的基础设施转变的过程，涵盖了对网络、存储、调度、操作系统、分布式原理等各个方面的容器化理解和改造。

最初大家就是租一批虚拟机，然后像管理物理服务器那样，用脚本或者手工的方式在这些机器上部署应用。但部署过程会碰到云端和本地环境不一致的问题，所以需要一套应用打包功能，而 dotCloud 公司开源的 Docker 使用镜像解决了这个问题，直接打包了应用以及运行所需要的整个环境，从而保证了本地和云端环境的强一致性，对其他 PaaS 公司实施了降维打击。

但一整套服务涉及很多的容器，所以随之而来的另一个问题就是容器的编排，于是爆发了一场关于容器编排的战争。Kubernetes(K8s) 凭借 Google 和 CNCF 社区的强力背书。击败了 Docker Swarm 和 Apache Mesos 等对手，成为云原生操作系统的事实标准，而 Docker Engine 这样一个只能用来创建和启停容器的小工具，最终只能充当这些平台项目的幕后英雄。

![20240626192601](https://image.zuoright.com/20240626192601.png)

虚拟机使用了 Hypervisor 硬件虚拟化技术，虚拟出包括操作系统在内的整个运行环境，比较消耗资源

而容器利用了 Linux 内核提供的 NameSpace 和 Cgroup 技术，仅仅是隔离出一个独立的进程，与宿主机共享内核，性能更好

凡是挑内核的场景都不应使用 Docker，比如不能做系统兼容性测试，也不能在 Docker 环境中驱动 IE 浏览器。

另外计算密集型业务，也不适合使用容器。
