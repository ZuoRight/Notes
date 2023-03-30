# UTM

官网：<https://mac.getutm.app/>

UTM 采用 Apple 的 Hypervisor 虚拟化框架，以接近本机的速度在 Apple Silicon 上运行 ARM64 操作系统，应该是目前最适合个人在 ARM64 架构平台（M1/M2/iOS）上免费使用的虚拟机软件，其底层是QEMU，不支持GPU虚拟化，即不能运行游戏

UTM 是永久且完全免费和开源的。GitHub 与 App Store 下载是一样的，后者可以获得自动更新，之所以收费下载目的等同于捐款。

![20221116132653](http://image.zuoright.com/20221116132653.png)

![20221116163555](http://image.zuoright.com/20221116163555.png)

![20221116164819](http://image.zuoright.com/20221116164819.png)

![20221116164901](http://image.zuoright.com/20221116164901.png)

![20221116164928](http://image.zuoright.com/20221116164928.png)

![20221116165127](http://image.zuoright.com/20221116165127.png)

![20221116165228](http://image.zuoright.com/20221116165228.png)

![20221116165347](http://image.zuoright.com/20221116165347.png)

![20221116165433](http://image.zuoright.com/20221116165433.png)

![20221116170950](http://image.zuoright.com/20221116170950.png)

## 安装桌面

```shell
sudo apt update
sudo apt install ubuntu-desktop  # 默认GNOME
sudo reboot
```

## 剪切板共享

```shell
# 需要安装spice-vdagent
sudo apt install spice-vdagent
# 命令行粘贴使用 Shift+Control+v，桌面环境不需要加Shift键
```

## 文件共享

![20221116203730](http://image.zuoright.com/20221116203730.png)

```shell
sudo mkdir /media/share  # create mount point
sudo mount -t 9p -o trans=virtio share /media/share -oversion=9p2000.L  # mount
```

## 安装VSCode

```shell
# 下载.deb包并放入共享文件夹：https://code.visualstudio.com/download
sudo apt install /meida/share/code_1.73.1-1667966450_arm64.deb
```
