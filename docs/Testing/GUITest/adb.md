# ADB

Android Debug Bridge, 一个 C/S 架构的 命令行工具，可以对 Android 应用进行安装、卸载及调试等

端口号: 5037

一些手机助手其实也是自带 adb 才能控制手机的，所以会占用 5037 端口

## 基础

```shell
# 查看连接设备
# 如果有多个设备，使用 -s udid 指定要连接的设备
adb devices

# 获取设备状态
adb get-state
`
三种状态
device 设备正常连接
offline 连接出现异常，设备无响应
unknown 没有连接设备
`

# 无线连接设备，速度较慢
adb tcpip
adb connect

# 结束/启动 adb 服务
adb kill-server
adb start-server

# 获取日志
adb logcat | grep START  # 获取包名和 Activity

# 收集 dumpsys、dumpstate、logcat 等数据，用于后续分析，比如耗电量
adb bugreport > path/bugreport.log

adb install 包名  # 将存放在主机上的apk安装到设备，加 -r 覆盖安装
adb uninstall 包名  # 卸载，加 -k 卸载时保存数据和缓存目录

# 复制手机文件到本地
adb pull sdcard/demo.txt path/  # 重命名 path/new_demo.txt
# 复制本地文件到手机
adb push path/demo.txt sdcard/

# 重启设备
adb reboot
`
adb bootloader  # 重启并进入 fastboot 模式，等价于 adb reboot-bootloader
adb recovery  # 重启并进入 recovery 模式，刷机时会用到
`

# 端口转发
adb forward tcp:1234 tcp:8888  # 将主机端口映射到设备的端口，方便调试

# 获取入口
adb logcat| grep -i displayed
aapt dump badging mobike.apk | grep launchable-activity
apkanalyzer
```

## ADB Shell

调用安卓系统自带的命令，通常存储在：`system/bin`, `sources/android-20/com/android/commands` 等位置

进入 `adb shell` 环境，或者直接 `adb shell pm xxx` 形式执行

- pm
- am
- dumpsys
- uiautomator
- input

### pm

Package Manager

```shell
pm list packages  # 获取所有应用的包名
`
命令后加参数
-s 列出系统应用
-3 列出第三方应用
-f 列出应用包名及对应的 apk 名及存放位置
-i 列出应用包名及其安装来源
`
# 还可以加过滤关键字，比如过滤 zhihu
adb shell pm list package -f -3 -i zhihu
# 输出：package:/data/app/com.zhihu.android-1.apk=com.zhihu.android  installer=com.xiaomi.market

pm dump 包名  # 获取包的各种信息，比如查看版本号：| grep version
pm path 包名  # 通过包名获取apk名

pm install 包名  # 安装存放在设备上的apk包
pm uninstall 包名

pm get-install-location  # 获取应用安装位置

pm clear com.xxx.xx  # 清空缓存数据，重置
```

### am

```shell
# 启动 Activity
am start 包名/.要启动的Activity
am start -W -n com.xxx.android/.view.WelcomeActivityAlias -S

am force-stop 包名  # 结束应用

am startservice  # 启动一个服务

# 监控 crash 与 ANR
am monitor
```

### dumpsys

```shell
# 获取所有的dumpsys子命令
dumpsys | grep -i dump

# 获取当前界面元素
dumpsys activity top

# 获取任务列表，可以获取到appium依赖的原始activity
dumpsys activity activities

# 获取特定包基本信息
dumpsys package com.xueqiu.android

# 获取系统通知
dumpsys notification

# 获得内存信息
dumpsys meminfo com.android.settings

# 获取cpu信息
dumpsys cpuinfo

# 获取gpu绘制分析
dumpsys gfxinfo com.android.settings

# 获取短信
dumpsys activity broadcasts | grep senderName
```

## 其它

```shell
adb shell screencap -p /sdcard/screen.png  # 截图
adb shell screenrecord sdcard/record.mp4  # 录屏
adb shell uiautomator dump  # 获取当前界面的控件信息
```
