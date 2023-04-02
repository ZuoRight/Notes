# 引言

## 计算机组成

根据图灵机和冯诺伊曼结构，计算机必须具备五大基本组成部件

- 输入设备：装载数据和程序
- 存储器：记住程序和数据
- 运算器：完成数据加工处理
- 控制器：控制程序执行
- 输出设备：显示处理结果

![20221119130345](http://image.zuoright.com/20221119130345.png)

![20221119181117](http://image.zuoright.com/20221119181117.png)

![20230204004035](http://image.zuoright.com/20230204004035.png)

## 主板

早期主板有两块比较重要的芯片：北桥和南桥。

北桥靠近CPU，负责与CPU、内存、显卡等高速设备通信，并与南桥连接

南桥负责与I/O（SATA、USB、LAN）、硬盘等低速设备通信

> SATA 一般指串行总线，负责主板和大容量存储装置（硬盘、光驱）之间传输数据

- old

![20221122011508](http://image.zuoright.com/20221122011508.png)

- new

现在北桥的功能已被集成到了CPU，南桥被PCH/FCH取代（或者说换了个名称：I/O Hub），通过DMI（Direct Media Interface）与CPU连接

![20221122010527](http://image.zuoright.com/20221122010527.png)

## CPU

- 实模式：可以随意执行全部CPU指令，随意直接通过物理地址访问内存
- 保护模式：将指令划分为ring0到ring3，调用CPU指令或者访问内存需要CPU和操作系统允许
- 长模式

中断，即通过硬件或软件的方式告诉CPU来执行一段特殊的代码。比如键盘输入，就是通过硬件中断的方式告知操作系统的

## 启动顺序

![20221124222816](http://image.zuoright.com/20221124222816.png)

启动固件(Fireware)固化在主板ROM芯片中，主要有早期的BIOS和最新的UEFI两种

引导加载程序(bootloader)是硬件和操作系统之间的接口

## BIOS

Basic I/O System，基本输入输出系统

![20221124223743](http://image.zuoright.com/20221124223743.png)

开机时，BIOS会做开机自检(P.O.S.T)，检测硬件正常后，按照CMOS设置里的顺序搜索引导设备（大概率是硬盘），挨个查看存储设备的前512字节是不是以0x55 0xAA结尾，如果不是，那就跳过找下一个设备；如果是的话，则表示这个磁盘可以启动，然后交给引导程序（Bootloader，比如GRUB2）

> 开机自检（Power On Self Test）会给出提示音，比如哔一声表示正常，长哔三声表示键盘错误，连续短哔可能RAM有问题

![20221124232639](http://image.zuoright.com/20221124232639.png)

CMOS最初是主板上一个单独的芯片，后来被集成到了南侨芯片组，它属于一种RAM，用于存储BIOS的配置（计算机的启动顺序、安装的磁盘驱动器类型、系统时钟的当前日期和时间等），所以也叫RTC(实时时钟)或NVRAM(非易失性RAM)，为避免计算机断电后恢复为默认值，所以CMOS有单独的纽扣电池提供电源（可以充电长达十余年）

![20221124224838](http://image.zuoright.com/20221124224838.png)

## UEFI

Unified Extensible Firmware Interface 统一可扩展固件接口

![20221124225800](http://image.zuoright.com/20221124225800.png)

UEFI不仅仅是BIOS的替换，本质上是一个运行在PC固件之上的微型操作系统，支持更友好的界面，还可以使用鼠标。

BIOS只能识别固定位置的磁盘引导块，而UEFI可以存储在主板的闪存中，也可以在启动时从硬盘或网络共享加载。

UEFI采用GPT分区表的方式后，硬盘容量和分区数目几乎没有上限（目前windows支持最大128个分区）

使用了安全引导，以防止加载未数字签名的驱动程序。在引导过程中，UEFI固件仅在实模式下初始化平台，平台初始化后，UEFI使用基本操作系统为后续启动启用64位保护模式。允许在启动过程中进行远程监视和控制，可防止恶意访问。

## USB

![20230329221305](http://image.zuoright.com/20230329221305.png)

## 内存和CPU

```shell
# 查看内存
free [-m]  # 静态，-m 用M为单位显示数据大小
top  # 动态

# 查看磁盘使用量
df -h

# 查看CPU
lscpu
"""
Architecture:            x86_64
  CPU op-mode(s):        32-bit, 64-bit
  Address sizes:         46 bits physical, 48 bits virtual
  Byte Order:            Little Endian
CPU(s):                  1
  On-line CPU(s) list:   0
Vendor ID:               GenuineIntel
  Model name:            QEMU Virtual CPU version (cpu64-rhel6)
    CPU family:          6
    Model:               13
    Thread(s) per core:  1
    Core(s) per socket:  1
    Socket(s):           1
    Stepping:            3
    BogoMIPS:            5199.99
    Flags:               fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pse36 clflush mmx fxsr sse sse2 ss syscall nx pdpe1gb rdtscp lm rep_good nopl xtopology cpuid tsc
                         _known_freq pni pclmulqdq ssse3 cx16 pcid sse4_1 sse4_2 x2apic popcnt aes xsave avx f16c rdrand hypervisor lahf_lm pti fsgsbase smep xsaveopt
Virtualization features: 
  Hypervisor vendor:     KVM
  Virtualization type:   full
Caches (sum of all):     
  L1d:                   32 KiB (1 instance)
  L1i:                   32 KiB (1 instance)
  L2:                    4 MiB (1 instance)
  L3:                    16 MiB (1 instance)
NUMA:                    
  NUMA node(s):          1
  NUMA node0 CPU(s):     0
Vulnerabilities:         
  Itlb multihit:         KVM: Mitigation: VMX unsupported
  L1tf:                  Mitigation; PTE Inversion
  Mds:                   Vulnerable: Clear CPU buffers attempted, no microcode; SMT Host state unknown
  Meltdown:              Mitigation; PTI
  Spec store bypass:     Vulnerable
  Spectre v1:            Mitigation; usercopy/swapgs barriers and __user pointer sanitization
  Spectre v2:            Mitigation; Retpolines, STIBP disabled, RSB filling
  Srbds:                 Not affected
  Tsx async abort:       Not affected
"""
```
