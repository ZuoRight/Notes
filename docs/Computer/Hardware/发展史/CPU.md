
> 人，思想与肉体的结合。机，软件与硬件的集合
>
> 人机之间本质的区别，大概是后者没有所谓的灵魂吧



我们已经知道，计算机是基于冯·诺依曼体系结构组成的，即：存储器，处理器（包含运算器和控制器）和输入输出设备。如果将计算机与人进行一个简单的类比，可能大致会如下所示：

> 骨架：主板
>
> > 头颅：北桥芯片
> >
> > > 大脑：处理器
> > >
> > > 记忆：内存
> > >
> > > 其它：显卡等
> > >
> >
> > 躯干：南桥芯片（Intel：PCH，AMD：FCH）
> >
> > > 胃：外存（软盘，硬盘等）
> > >
> > > 其它：网卡等
> >
> > 神经：总线，负责CPU和其他设备连接
> >
> > > 系统总线
> > >
> > > 内存总线
> > >
> > > I/O总线
> >
> > 心脏：电源
> >
> > 血管：电路
>
> 四肢和五官：输出输入设备（鼠标，键盘，显示器，打印机等）
>
> 肌肉：驱动
>
> 关节：端口

计算机主板（Motherboard，简称：Mobo），又称系统板，或者母版。就像人的骨架一样，连接着身体的各个部位。头颅和躯干，上北下南，类似主板芯片组（Chipset）的北桥和南桥两大部分。北桥，主要负责连接运行速度较快的处理器，内存与显卡等组件。由于处理器需要时刻与内存交换数据，以至于目前主流的架构中已经将内存控制器整合到了处理器之中，也就慢慢的不再有北桥这个概念，就像我们平时说“脑袋”而不是“头颅”这个词一样，因为大家都知道脑袋已经暗含了大脑和记忆等含义在里面。南桥呢，主要负责连接运行速度较慢的设备接口，包括硬盘，网卡，以及各种输入输出设备等。



（主板图片）



世界三大主板制造商，了解一下：

> 华硕（ASUS）
>
> 技嘉（GIGABYTE）
>
> 微星（MSI）

值得一提的是，它们全部来自中国台湾，其实再往后排也还是一样的，好板子，中国造，你应该知道。



通过与人之间的类比，我们可以抽象出计算机的整体运行流程：接通电源，电流经由电路传输到各个组件形成回路，启动计算机，通过输入设备获取数据，数据经过驱动和端口，被传输到存储器，再通过总线传输到处理器，处理器对数据进行运算和处理，再交由总线传回到存储器，然后被处理过后的数据通过输出设备输出，往复循环。



（冯诺依曼结构图）



毫无疑问，在心脏保持正常跳动的情况下，大脑是一个人最重要的部分。同理，计算机的大脑：中央处理器（Central Processing Unit），简称 CPU，也是计算机最核心的组成部分。



从外部来看，CPU 就是一块无比精细的印刷电路板。它主要可被划分为三个部分：控制单元，数据单元，运算单元。

在控制单元中，有一个指令指针寄存器，存放着被设计好的指令集。指令集主要分为复杂指令集（CISC）和精简指令集（RISC）。

使用复杂指令集的 CPU 主要为以 x86 架构为代表的的个人电脑中，主要有以下厂商：



CPU：X86架构

> 创造者：
>
> > 霍夫
> >
> > 费金
> >
> > 卖卓尔
>
> 品牌
>
> > AMD
> >
> > Intel
> >
> > > 4004，8086，x86
> > >
> > > 赛扬
> > >
> > > 奔腾
> > >
> > > 酷睿
>
> 组成：
>
> > 电路：提供电源，提供驱动CPU运转的时钟信号
> >
> > > 时钟发生器：内含晶振，发出电信号（电压高低的变化）
> >
> > 寄存器：不同CPU不一样
> >
> > > 累加器：运算的核心
> > >
> > > 标志寄存器：存储运算结果的状态，比如是否发生了进位，数字大小的比较结果
> > >
> > > **指针/程序计数器（PC）**：存储内存地址，该地址指向下一条即将执行的指令，它的值会跟随时钟信号自动更新，即每执行完一条指定就会自动更新为下一条指令的地址，可以说程序就是靠PC寄存器的值不断变化而运行的
> > >
> > > 栈顶指针（SP）：用于在内存中创建出一块成为“栈”的临时数据存储区域
> >
> > 运算器
> >
> > 控制器
> >
> > 协处理器：超算用的，家电不需要，手机上用来处理传感器数据达到省电目的



GPU

> 英伟达





使用精简指令集的 CPU 主要为 ARM 架构

由于空间较小，手机的处理器并不是单独的一个部分，而是CPU与GPU，DSP，Model，GPS等各种模块集成在一个系统级芯片内，叫做SoC（System on Chip）



手机主板

> SoC
>
> 射频部分
>
> 基带部分
>
> 其他部分



CPU：

> ARM
>
> > ARM 1~10
> >
> > Cortex：从 ARM 11 开始改名
> >
> > > Cortex-A
> > >
> > > Cortex-R
> > >
> > > Cortex-M



GPU

> Qualcomm：Adreno
>
> ARM：Mali



SoC目前主要有：

> 高通骁龙（Qualcomm）
>
> 苹果A系
>
> 华为海思麒麟
>
> 三星猎户座
>
> 联发科MTK







<https://12101111.github.io/diannao110/motherboard/%E4%B8%BB%E6%9D%BF.html>



<https://time.geekbang.org/column/article/91793>组成

<https://time.geekbang.org/column/article/89417>协作





电脑外设：有线像无线方向发展

> 输入设备
>
> > 鼠标
> >
> > 键盘：池老师那篇文章
> >
> > > 青轴
> > >
> > > 黑轴
> > >
> > > 茶轴
> > >
> > > 红轴
> >
> > 扫描仪
>
> 输出设备：
>
> > 显示器
> >
> > 色差
> >
> > > LCD
> > >
> > > LED
> > >
> > > IPS
> > >
> > > PLS
> > >
> > > RETINA
> >
> > 接口
> >
> > 打印机


电脑的工作原理

程序的运行原理



参考：

1. 鸟哥
2. 组成原理前两篇
3. linux
4. 架构



骨架：主板

心脏：电源

血管与神经：电路

脑袋：CPU+内存+硬盘

四肢+五官：输入+输出



影像：显卡



信息：数据

能量：程序



容量单位：字节

速度单位：赫兹，bit/s





这些东西是怎么连接合作运行的呢

x86架构：总线，

端口：关节

驱动：肌肉



二进制，莱布尼茨，继电器，真空管，晶体管（二极管：肖克利，三极管：巴恩和布拉顿），集成电路（即芯片：罗伯特·诺伊斯），摩尔定律不再适用，以上都是经典计算机，量子计算机



<https://time.geekbang.org/column/article/89417?utm_source=infoq&utm_medium=sitenavigation>



先刻画大的轮廓，再分别越来越具体



CPU指令集：x86架构和ARM架构

dsp


存储程序计算机：可编程，可存储

不可存储：插线

不可编程：程序写死


人控制机器做事，需要机器指令：编程语言和编译器-汇编-》

操作系统：解决多个软件在同一电脑上共处
