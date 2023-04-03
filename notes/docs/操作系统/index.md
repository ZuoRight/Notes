# 引言

主板加电，CPU 进入实模式，执行 ROM 中的 BIOS 或 UEFI 启动固件

启动固件检测正常后，搜寻存储器找到启动盘，加载 MBR 或 ESP 到内存，启动引导加载程序，加载硬盘分区中的 OS 文件

## 引导加载程序

引导加载程序 Bootloader 位于 MBR 或 ESP 中， 用于从多操作系统的计算机中选择一个系统来启动，或从系统分区中选择特殊的内核配置，是硬件和操作系统之间的接口

![20221124222816](http://image.zuoright.com/20221124222816.png)

Linux 发行版中可使用的 Bootloader 有三种

- `GRUB` GRand Unified Bootloader
- `GRUB2` 最新且最常用
- `LILO`

GRUB2，包含`boot.img`(启动代码)和`core.img`(内核镜像)

`core.img` 主要包括以下模块

- `diskroot.img`
- `lzma_decompress.img` 解压缩程序
- `kernel.img`

### 加载顺序

1. `boot.img` 加载 `core.img` 的 `diskroot.img`
2. `diskroot.img` 加载 `lzma_compress.img`
3. CPU切换到保护模式，启用分段（辅助进程管理），启动分页（辅助内存管理），打开其他地址线
4. `lzma_compress.img` 解压运行 `kernel.img`，解析 `/boot/grub2/grub.cfg`(GRUB2配置文件)，选择操作系统，检查通过后加载完整系统内核
