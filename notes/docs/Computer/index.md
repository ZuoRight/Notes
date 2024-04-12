# 引言

[Interactive Map of Linux Kernal](https://makelinux.github.io/kernel/map/)

![20240412142557](https://image.zuoright.com/20240412142557.png)

## 操作系统

- 批处理系统
    - 单道批处理系统
    - 多道批处理系统
- 分时系统，通用操作系统，unix 1970
- 实时系统：特定领域
    - 硬实时系统：飞行自动控制
    - 软实时系统：飞机订票/导弹制导/银行管理系统
    - VxWorks 

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

### USB

![20230329221305](http://image.zuoright.com/20230329221305.png)
