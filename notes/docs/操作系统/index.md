# 引言

操作系统启动过程

1. 主板加电，CPU 进入实模式，执行 ROM 中的 BIOS
2. BIOS 检测正常后，搜寻存储器找到启动盘，加载引导扇区的主引导记录（MBR）到内存，启动引导程序（比如GRUB2），选择内核和指定版本
3. `boot.img` 加载 `core.img` 的 `diskroot.img`
4. `diskroot.img` 加载 `lzma_compress.img`
5. CPU切换到保护模式，启用分段（辅助进程管理），启动分页（辅助内存管理），打开其他地址线
6. `lzma_compress.img` 解压运行 `kernel.img`，解析`/boot/grub2/grub.cfg`(GRUB2配置文件)，选择操作系统，检查通过后加载完整系统内核
7. 加载系统内核 `Kernel`
8. `系统初始化`
9. `init` 或 `systemd` 1号进程
10. `Shell`

在内核完成了操作系统的各种初始化之后，会从缺省路径尝试启动第一个用户态进程，即1号进程，也叫初始化守护进程，然后操作系统便从内核态切换到了用户态。

当 Shell 启动了某个进程，会调用某个系统API（e.g. `tcsetpgrp`）把进程编号PID与Shell所属的终端关联起来，当终端需要发送信号时，会调用某个系统API（`tcgetpgrp`）获取进程的编号
