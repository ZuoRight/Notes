# Android

Android 操作系统是一种多用户 Linux 系统，其中的每个应用都是一个不同的用户。默认情况下，系统会为每个应用分配一个唯一的 Linux 用户 ID，每个应用都在其自己的 Linux 进程内运行，每个进程都拥有自己的虚拟机。

- 平台架构：<https://developer.android.com/guide/platform>
- 工具：<https://developer.android.com/studio/command-line#tools-sdk>

Google 的 Android 系统，其实指的是 AOSP + GMS（谷歌移动服务）。AOSP 是开源的，谷歌移动服务是闭源的，华为只是无法使用谷歌地图等谷歌定制应用。

另外「Android」文字及 Logo 属于 Google 公司的商标，需取得谷歌的认可才能进行商业使用。

## SDK

Software Development Kit 软件开发工具包

可单独下载，也可以通过[Android Studio](https://developer.android.google.cn/studio/intro)下载

Android SDK 工具会将您的代码连同任何数据和资源文件编译成一个 APK（Android 软件包），即带有 .apk 后缀的归档文件。一个 APK 文件包含 Android 应用的所有内容，它也是 Android 设备用来安装应用的文件。

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

## [应用基础](https://developer.android.com/guide/components/fundamentals)

应用组件是 Android 应用的基本构建块。每个组件都是一个入口点，系统或用户可通过该入口点进入您的应用，共有四种应用组件类型，每种类型都有不同的用途和生命周期。

- Activity `<activity>`
- 服务 `<service>`
- 广播接收器 `<receiver>`
- 内容提供程序 `<provider>`

所有组件必需使用相应的元素在清单文件(`AndroidManifest.xml`)中声明，该文件必须位于应用项目目录的根目录中。清单文件的主要任务是告知系统应用组件的相关信息。

[布局](https://developer.android.com/guide/topics/ui/declaring-layout)定义了应用中的界面结构，布局中的所有元素均使用 View 和 ViewGroup 对象的层次结构进行构建。View 通常称为微件，用于绘制用户可看到并与之交互的内容。ViewGroup 则是不可见的容器，用于定义 View 和其他 ViewGroup 对象的布局结构。

## 模拟器

安装模拟器建议选择x86 Images标签下x86_64带Google APIs的

启动模拟器：`emulator @android6`

如果想让模拟器联网需要从命令行启动并配置DNS

参考：<https://www.jianshu.com/p/cb738ad177ac>

`emulator -avd 模拟器名 -dns-server 202.106.0.20`

## 逆向反编译

### SSL-Pinning

1. 客户端内置证书，即在客户端内置仅接受指定域名的根证书，而不接受操作系统或浏览器内置的CA根证书对应的任何证书
2. 客户端内置公钥

### 突破 SSL-Pinning

<https://zhuanlan.zhihu.com/p/60392573>

内置证书或者公钥的时候，需要对比验证客户端和服务端的证书或公钥，一般是通用的API，所以可以直接控制这个API的返回结果让验证通过。

1. Xposed + justTrustme：不行？
2. apk 反编译工具
3. 使用逆向神器 Frida：太难？

<https://blog.csdn.net/xiaohua_de/article/details/80259920>

使用模拟器（首选逍遥模拟器），模拟器不行，使用真机，需要 root，root 工具（首选刷机精灵）
