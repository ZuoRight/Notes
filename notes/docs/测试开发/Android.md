# Android

配置环境变量

```shell
# Android
# 可通过SDK Manager获取SDK所在路径
export ANDROID_HOME=/Users/7c/Library/Android/sdk
# 注意顺序：/emulator要在/tools前面，不然会报错
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/tools/bin
```

Android Studio：<https://developer.android.google.cn/studio/intro>

命令行工具：<https://developer.android.com/studio/command-line#tools-sdk>

平台架构：<https://developer.android.com/guide/platform>

应用基础：<https://developer.android.com/guide/components/fundamentals>

## SDK

Software Development Kit 软件开发工具包

<https://www.cnblogs.com/kangjianwei101/p/5621238.html>

## 模拟器

安装模拟器建议选择x86 Images标签下x86_64带Google APIs的

`emulator @android6`

## UI Automator

- `/tools/bin/uiautomatorviewer` 查看界面布局、层次结构、组件属性等

## adb
