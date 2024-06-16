# 引言

[计算机硬件历史](https://en.wikipedia.org/wiki/History_of_computing_hardware)

- 真空电子管 -> 晶体管 -> 集成电路
- 大型 -> 小型 -> 微型
- 军用 -> 商用 -> 民用

## 操作系统

- 无：计算机早期，操作人员通过控制面板和纸带等方式手动输入指令
- 批处理系统
    - 单道批处理系统
    - 多道批处理系统
- 分时系统
    - 早期：CTSS、Multics
    - 通用操作系统：UNIX(1970)、CP/M(1974)

UNIX 最初为大型计算机和小型工作站设计，目标是提供一个多用户、多任务的操作系统，主要用于科研、学术和工程领域

CP/M 主要为微型计算机设计，面向个人用户和小型企业，旨在为个人计算机提供一个简单易用的操作环境，是单用户单任务的。

- 实时系统：特定领域
    - 硬实时系统：飞行自动控制
    - 软实时系统：飞机订票/导弹制导/银行管理系统
    - VxWorks

### UNIX

<https://zh.wikipedia.org/wiki/UNIX>

20 世纪 60 年代中期，麻省理工学院、AT&T 公司的贝尔实验室、以及通用电气正在为大型机 GE645 开发一个分时操作系统 Multics，由于规模太大，过于复杂，所以贝尔实验室的肯·汤普森和丹尼斯·里奇等人选择退出，并决定将经验用于一个小型的新项目，于是诞生了 UNIX。最初由汇编语言编写，后面创造出了 C 语言重新实现。

起初，AT&T 公司以低廉甚至免费的许可将 Unix 源码授权给学术机构做研究或教学之用，许多机构在此源码基础上加以扩展和改进，衍生出各种 Unix-Like 系统，它们之间互相借鉴。

> 为了维护不同操作系统与用户程序之间的兼容性，IEEE 计算机协会基于现有的 UNIX 实践和经验，制定一系列标准，称作 POSIX（可移植操作系统接口）

BSD 贡献了许多网络相关和系统级的工具，而 GNU 则提供了大量的用户级工具和应用程序，Linux 主要提供了内核，由 Linus Torvalds 开发。

- [Unix timeline](https://commons.wikimedia.org/wiki/File:Unix_timeline.en.svg#/media/File:Unix_timeline.en.svg)
- [Unix history simple](https://zh.wikipedia.org/wiki/File:Unix_history-simple.svg)

![20220707143751](http://image.zuoright.com/20220707143751.png)

### LINUX

LINUX 本身只是一个开源的内核(Kernel)

[Interactive Map of Linux Kernal](https://makelinux.github.io/kernel/map/)

![20240412142557](https://image.zuoright.com/20240412142557.png)

内核主要负责一些最基本的工作，比如：管理硬件驱动、管理内存、管理文件系统、管理进程等等。

内核是计算机资源的管理者，通过系统 API 函数向用户应用软件提供一些接口，会在处理器的特权模式（宏内核模式）下运行。

微内核架构正好与宏内核架构相反，它提倡内核功能尽可能少：仅仅只有进程调度、处理中断、内存空间映射、进程间通信等功能，这样的内核是不能完成什么实际功能，开发者们把实际的进程管理、内存管理、设备管理、文件管理等服务功能，做成一个个服务进程。和用户应用进程一样，只是它们很特殊，宏内核提供的功能，在微内核架构里由这些服务进程专门负责完成。微内核定义了一种良好的进程间通信的机制：消息。

`uname -a`

![20230401113035](http://image.zuoright.com/20230401113035.png)

内核版本：主版本号.次版本号.末版本号

> 次版本号，奇数为开发版，偶数为稳定版  
> <https://www.kernel.org/>

![20220707142531](http://image.zuoright.com/20220707142531.png)

### DOS

<https://en.wikipedia.org/wiki/MS-DOS>

1981 年，微软受 IBM 委托，为其 PC 机开发操作系统，彼时 Tim Paterson 基于 CP/M 改进开发出了 86-DOS，被微软购买后改名为 MS-DOS 卖给了 IBM 进行发售，DOS 后来又参考了 UNIX 的文件系统等，并不断改进，最后演变为了 Windows 系统。

## 硬件组成

根据图灵机和冯诺伊曼结构，计算机必须具备五大基本组成部件

- 输入设备：装载数据和程序
- 输出设备：显示处理结果
- 存储器：记住程序和数据
- 控制器：控制程序执行
- 运算器：完成数据加工处理

![20221119130345](http://image.zuoright.com/20221119130345.png)

![20221119181117](http://image.zuoright.com/20221119181117.png)

![20230204004035](http://image.zuoright.com/20230204004035.png)

### 主板

早期主板有两块比较重要的芯片：北桥和南桥。

北桥靠近CPU，负责与CPU、内存、显卡等高速设备通信，并与南桥连接

南桥负责与I/O（SATA、USB、LAN）、硬盘等低速设备通信

> SATA 一般指串行总线，负责主板和大容量存储装置（硬盘、光驱）之间传输数据

- old

![20221122011508](http://image.zuoright.com/20221122011508.png)

- new

现在北桥的功能已被集成到了CPU，南桥被PCH/FCH取代（或者说换了个名称：I/O Hub），通过DMI（Direct Media Interface）与CPU连接

![20221122010527](http://image.zuoright.com/20221122010527.png)

### I/O

- 文件I/O：读取或写入文件
- 数据库I/O：从数据库读取或写入数据
- 网络I/O：通过网络发送或接收数据

### USB

![20230329221305](http://image.zuoright.com/20230329221305.png)
