# 手机相关

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
2. 下载带 VirtualBox 的版本
3. 安装 -> 登陆 -> 添加/下载镜像

需要开启 CPU 的虚拟化功能：BIOS -> VT-x

### MuMu

网易游戏开发的模拟器

<https://mumu.163.com>

## 手机投屏到电脑

### iPhone

借助 QuickTime Player 实现

手机 USB 连接电脑，新建影片

![20240726223146](https://image.zuoright.com/20240726223146.png)

### Android

可以使用 Android Studio 的 [设备镜像](https://developer.android.com/studio/run/device?hl=zh-cn) 功能实现

在 Running Devices 或 Device Manager 窗口选择一个设备手动开启

> 前提，要先打开一个 Android 项目进入编辑窗口才能看到如下选项，可以随便创建一个空项目或示例项目

![20240726221509](https://image.zuoright.com/20240726221509.png)

也可以在 Settings -> Tools -> Devices Mirroring 这里设置自动开启条件，比如可以设置「连接设备就自动启动镜像」

如果您使用的是搭载 Android 12 的特定设备（例如 Redmi K40、Poco F3 或 Mi 11X），则必须同时启用「USB 调试」和「USB 调试（安全设置）」这两项设置，然后重启设备，才能启用设备镜像

当然也有一些第三方工具可以实现，但是要么收费，要么卡，要么分辨率太低

## 访问手机文件

### Device File Explorer

使用 Android Studio，View > Tool Windows > Device File Explorer 查看

### http.server

另外还可使用 [Termux](https://termux.dev/) 命令行工具在手机上启动一个服务

比如电脑要访问 Android 手机的截图，

```shell
termux-setup-storage  # 开启访问权限
cd /sdcard/DCIM
python -m http.server 8080
```

电脑访问 `http://手机IP地址:8080` 即可查看

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

## Google Play

小米手机为例

1. 连接 VPN
2. 小米设置中打开谷歌基础服务（这一步貌似可选）
3. 小米应用商店中是可以搜索到 Google Play 商店的，只需要升级
4. 登录 Google 账号即可
