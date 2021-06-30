# Android

Android Studio：<https://developer.android.google.cn/studio/intro>

命令行工具：<https://developer.android.com/studio/command-line#tools-sdk>

平台架构：<https://developer.android.com/guide/platform>

应用基础：<https://developer.android.com/guide/components/fundamentals>

## SDK

Software Development Kit 软件开发工具包

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

[参考](http://www.cnblogs.com/nbkhic/p/3806886.html)
1. uiautomatorviewer.bat
  主要用来查看控件的属性，比如resource id，class name等
  也可查看被测app的appPackage（Desired Capabilities中使用）
2. monitor.bat
  该工具可以帮我们找到android控件的content-description，为以后的find_element_by_accessibility_id 定位方法做参数使用

## 模拟器

安装模拟器建议选择x86 Images标签下x86_64带Google APIs的

`emulator @android6`
