# 手机相关

## 手机投屏到电脑

- Android

借助 AirtestIDE 实现，数据线连接电脑后，会显示设备，点击 connect 即可

- iPhone

借助 QuickTime Player 实现

## 模拟器

### emulator

`SDK/emulator`

安装模拟器建议选择 x86 Images 标签下带 Google APIs 的 x86_64

启动模拟器：`emulator @android6`

如果想让模拟器联网需要从命令行启动并配置 DNS

参考：<https://www.jianshu.com/p/cb738ad177ac>

`emulator -avd 模拟器名 -dns-server 202.106.0.20`

### Genymotion

提供了 Android 虚拟环境，需要虚拟机的配合，免费版不提供短信和电话这些测试功能

1. 注册账号
2. 下载带 virtualBox 的版本
3. 安装 -> 登陆 -> 添加/下载镜像

需要开启 CPU 的虚拟化功能：BIOS -> VT-x

## 刷机

```shell
adb root  # 以 root 权限重启 adb
# 输入 su，若 $ 变 # 则表明已 root，否则报错

adb bootloader  # 重启并进入 fastboot 模式，等价于 adb reboot-bootloader
adb recovery  # 重启并进入 recovery 模式，刷机时会用到
```

Root，即获取根权限

BootLoader，是启动操作系统内核前运行的小程序，BL 锁住则不能刷入 Recovery，也就不能刷机

Recovery 模式，可以对手机内部数据或系统进行修改的模式，该模式下，可以备份和升级系统，只有刷入了第三方的 Recovery 才可以刷入第三方的 ROM 包，手机系统自带的 Revovery 只能刷官方提供的 ROM

### Pixel 刷机步骤

1. 镜像下载后解压，<https://developers.google.cn/android/images?hl=zh-cn#instructions>

![20240720113516](https://image.zuoright.com/20240720113516.png)

2. 进入 fastboot 模式：`adb reboot bootloader`，同时按住「电源键 & 音量减」也可进入 bootloader 界面
3. 解锁 bootloader

![20240720113342](https://image.zuoright.com/20240720113342.png)

4. 开始刷机：`flash-all.bat`，刷完后可能提示是否清除 data 目录，选择 yes，成功后查看系统设置的版本号确认是否正确

## 特殊拨号

```text
*#06# 显示手机原厂出厂串号（IMEI），鉴别真伪，仿冒山寨机现形，打开电池盖对照
*#*#4636#*#* 手机信息、耗电情况、使用统计数据、WiFi 信息
*#*#3646633#*#* 工程模式
```
