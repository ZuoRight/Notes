# ADB

```shell
adb kill-server
adb devices
adb connect
adb tcpip 5555

adb logcat | grep START  # 获取包名和Activity

adb shell pm list packages  # 获取所有包名
adb shell pm path 包名  # 通过包名获取apk名

adb shell am start 包名/.要启动的Activity  # 启动Activity
adb shell am force-stop 包名  # 关闭
```
