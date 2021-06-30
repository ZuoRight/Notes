# Android

Android Studio：<https://developer.android.google.cn/studio/intro>

命令行工具：<https://developer.android.com/studio/command-line#tools-sdk>

平台架构：<https://developer.android.com/guide/platform>

应用基础：<https://developer.android.com/guide/components/fundamentals>

## SDK

Software Development Kit 软件开发工具包

```shell
# Android
# 注意顺序：/emulator要在/tools前面，不然会报错
export ANDROID_HOME=/Users/7c/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/tools/bin
```

## 模拟器

安装模拟器建议选择x86 Images标签下x86_64带Google APIs的

`emulator @android6`
