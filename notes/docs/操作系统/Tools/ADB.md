# ADB

```shell
adb kill-server
adb devices

# 如果有连接多个设备可以用-s udid 指定要控制的设备

adb connect
adb tcpip 5555

adb logcat | grep START  # 获取包名和Activity

adb shell pm list packages  # 获取所有包名
adb shell pm dump 包名| grep version  # 查看版本号
adb shell pm path 包名  # 通过包名获取apk名

adb shell am start 包名/.要启动的Activity  # 启动Activity
adb shell am force-stop 包名  # 关闭
```
