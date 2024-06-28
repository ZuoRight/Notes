# ADB

```shell
# 查看连接设备
adb devices

# 如果有连接多个设备可以用-s udid 指定要控制的设备

# 无线连接设备
adb tcpip
adb connect

# 关闭adb后台进程
adb kill-server

# 查看日志
adb logcat | grep START  # 获取包名和Activity

# 收集日志数据，用于后续分析，比如耗电量
adb bugreport

# 获取当前界面元素
adb shell dumpsys activity top
# 获取任务列表
adb shell dumpsys activity activities

# 获取入口
adb logcat| grep -i displayed
aapt dump badging mobike.apk | grep launchable-activity
apkanalyzer

adb shell
    pm
    am
    dumpsys
    uiautomator
    input

adb shell pm list packages  # 获取所有包名
adb shell pm dump 包名| grep version  # 查看版本号
adb shell pm path 包名  # 通过包名获取apk名

adb shell am start 包名/.要启动的Activity  # 启动Activity
adb shell am start -W -n com.xxx.android/.view.WelcomeActivityAlias -S
adb shell am force-stop 包名  # 关闭

# 清空缓存数据，重置
pm clear com.xxx.xx
```

- 性能统计 dumpsys

![20230902230707](https://image.zuoright.com/20230902230707.png)
