# Android

Android 操作系统是一种多用户 Linux 系统，其中的每个应用都是一个不同的用户。默认情况下，系统会为每个应用分配一个唯一的 Linux 用户 ID，每个应用都在其自己的 Linux 进程内运行，每个进程都拥有自己的虚拟机。

Google 的 Android 系统，其实指的是 AOSP + GMS（谷歌移动服务）。GMS 是闭源的，但 [AOSP](https://source.android.com/docs/setup/about?hl=zh-cn) 是开源的，华为只是无法使用谷歌地图等谷歌定制应用。

> Android Logo 及文字属于 Google 公司的商标，需取得谷歌的认可才能进行商业使用。

- 平台架构：<https://developer.android.com/guide/platform>

![20241222000900](https://image.zuoright.com/20241222000900.png)

- 版本

<https://developer.android.com/about/versions?hl=zh-cn>

- 测试

<https://developer.android.com/training/testing?hl=zh-cn>

## IDE

### Eclipse ADT

在 Android Studio 诞生之前，Eclipse 是 Google 官方推荐的 Android 开发 IDE

Eclipse 使用了 ADT (Android Development Tools) 插件为 Eclipse 提供了 Android 开发的支持

DDMS 曾作为 ADT 插件的一部分集成在 Eclipse 中，提供了一个图形化界面，方便开发者使用各种调试工具，例如查看线程、内存使用情况、获取屏幕截图、监控网络流量等

随着 Android Studio 的推出，Google 停止了对 Eclipse ADT 的官方支持。

### Android Studio

<https://developer.android.google.cn/studio/intro>

![20241222140938](https://image.zuoright.com/20241222140938.png)

> 下载插件或编译时若遇到 `java.net.ConnectException: Connection refused` 可设置 HTTP 代理解决

```text
Platform 平台版本管理
    必须安装至少一个版本的 Android 平台，才能编译应用
emulator 模拟器
    emulator
    mksdcard
Tools
    /Command-Line Tools 命令行工具
        sdkmanager
        apkanalyzer
        avdmanager
        lint
        retrace
    /Build-Tools 构建工具
        AAPT2
        apksigner
        zipalign
    Platform-Tools 平台工具
        adb
        logcat
        fastboot
        etc1tool
```

## Android SDK

<https://developer.android.com/studio/command-line#tools-sdk>

Software Development Kit 软件开发工具包，依赖 JDK

- 通过 Android Studio 下载管理

配置环境变量

```shell
# 在 SDK Tools 中获取 SDK 所在路径
export ANDROID_HOME=/Users/xxx/Library/Android/sdk
# 注意顺序：/emulator 要在 /tools 前面，不然会报错
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/tools/bin
```

- 使用命令行工具 sdkmanager 直接下载

<https://developer.android.com/tools/sdkmanager?hl=zh-cn>

- 只下载 Platform-Tools

如果只想使用 ADB，则可以只下载 Platform-Tools 即可

<https://developer.android.com/tools/releases/platform-tools?hl=zh-cn>

## 开发

开发者指南：<https://developer.android.com/guide?hl=zh_cn>

![20241222151948](https://image.zuoright.com/20241222151948.png)

示例

New Project -> Empty Activity -> Finish

![20241222145858](https://image.zuoright.com/20241222145858.png)

Build 后会生成 Android 视图

![20241222164320](https://image.zuoright.com/20241222164320.png)

```text
/kotlin+java  写 Kotlin 或 Java 代码的地方，业务功能都在这里实现
/res
    1.所有的资源文件都会在 R.java 文件下生成对应的资源id（R文件可以理解为字典，res下每个资源都都会在这里生成一个唯一的id）
    2.可以直接通过资源id访问到对应的资源
/assets  与 res 的区别是，不会在 R.java 文件下生成对应的资源id，需要我们通过 AssetManager 以二进制流的形式来读取
/MainActivity.java
/AndroidManifest.xml  配置文件
/activity_main.xml  布局文件
```

Android SDK 工具会将应用的代码、数据、以及资源文件等所有内容编译成一个 `.apk` 后缀的归档文件，APK（Android Application PacKage）可安装在安卓设备中。

```text
META-INF  存放签名文件签名信息的目录，用于系统签名校验
res  存放资源文件的目录，包含项目中的 layout（布局）和图片资源等
AndroidManifest.xml  应用的全局配置文件
classes.dex 由 Java 产生的字节码文件（.class）打包生成为虚拟机可以运行的字节码文件（.jar）再压缩而成（.dex）
    为什么有 3 个 dex 文件，引用太多外部 jar 包的时候会导致方法数过多，超了 Android 系统设定 65k 方法的限制，也就是我们经常说的 95536 方法数限制，所以要使用多 dex 打包
resources.arsc  资源文件的ID索引表，如 layout、drawable、mipmap 都会在 R 文件生成相应的ID资源
assets  原始资源文件夹，一般用于存放原始的网页、音频等
lib  引用的第三方 sdk 的 so 文件
```

## [应用基础](https://developer.android.com/guide/components/fundamentals)

应用组件是 Android 应用的基本构建块。每个组件都是一个入口点，系统或用户可通过该入口点进入您的应用，共有四种应用组件类型，每种类型都有不同的用途和生命周期。

- Activity `<activity>`
- 服务 `<service>`
- 广播接收器 `<receiver>`
- 内容提供程序 `<provider>`

所有组件必需使用相应的元素在清单文件 `AndroidManifest.xml` 中声明，该文件必须位于应用项目目录的根目录中。清单文件的主要任务是告知系统应用组件的相关信息。

[布局](https://developer.android.com/guide/topics/ui/declaring-layout)定义了应用中的界面结构

布局中的所有元素均使用 View 和 ViewGroup 对象的层次结构进行构建

View 通常称为微件，用于绘制用户可看到并与之交互的内容

ViewGroup 则是不可见的容器，用于定义 View 和其他 ViewGroup 对象的布局结构

## 逆向工程

通过动态加静态分析全面理解程序的运行机制，包括代码逻辑、加密算法、协议等，可能涉及

- 静态分析（反编译、字符串搜索、控制流分析）
- 动态调试（Frida Hook、内存 Dump、网络抓包）
- 行为监控（文件操作、系统调用追踪）

反编译是逆向的基础步骤，若代码被混淆或加密，需用 Frida Hook 方法参数，或内存 Dump 解密数据

比如破解某 App 的 VIP 验证：反编译 APK → 发现校验函数被混淆 → Frida Hook 该函数 → 分析返回值规律 → 修改 Smali 代码绕过验证 → 重打包签名

Frida Hook 是一种动态代码注入技术，通过注入 JavaScript/Python 脚本到目标进程内存中，实时 Hook（钩住）函数调用，在运行时拦截和修改 Android/iOS/Windows 等平台的应用行为，无需修改原始程序文件

```text

场景	        技术实现
绕过 Root 检测	Hook SystemProperties.get() 返回假值
破解加密算法	 拦截 javax.crypto.Cipher.init() 提取密钥
网络协议逆向	 Hook OkHttp/HttpURLConnection 捕获请求/响应
游戏修改	    修改 Unity/Cocos2d 游戏的内存数值（金币/血量）
脱壳	        Dump 内存中的解密 DEX（如针对梆梆加固）
```

## 反编译 APK

直接把 APK 解压是没有办法进行阅读的，因为在打包这个过程中经过了 build-tools 打包工具处理

可以通过反编译将 APK 拆成我们可以阅读的文件

反编译前查看有没有加固或混淆，即所谓的壳，如果有壳需要先脱壳，否则直接反编译得到的是壳的代码，而非原始逻辑

> 加固后安装包存在的诸多问题，所以在一些大厂的应用中都没发现有加固的

- 查壳（ApkScan-PKID / 摸瓜）→ 确认加固类型
- 脱壳（Frida / drizzleDumper）→ 获取原始 DEX
    - Frida-DEXDump：基于 Frida 动态脱壳，适用于多数加固方案
    - drizzleDumper：针对特定加固（如腾讯、梆梆）的脱壳工具
    - 手动脱壳：调试分析壳的加载流程，提取解密后的 DEX
- 反编译（Jadx / Apktool）→ 分析代码逻辑

### ClassyShark

推荐，一键反编译，可直接浏览 APK 中的 xml 文件等，支持对 `.dex`, `.aar`, `.so`，`.apk`, `.jar`, `.class` 等文件的操作

下载` ClassyShark.jar` 后直接点击使用就可以了

它可以直观的看到 APK 下每个包中的方法数，并且以直观的扇形图展示在右边

可以看到 APK 下用到的第三方框架

可以直接看到所使用的 xml 文件等布局文件，同时你也可以看到 APK 下所包含的方法数是多少以及它使用的开源库等信息

### [Apktool](https://apktool.org/docs/install/)

拆解包，获取 res 和 xml 等资源

### [dex2jar](https://github.com/pxb1988/dex2jar/releases)

将 `classes.dex` 转换成 `jar` 文件

1. `.apk` 改为 `.zip` 或 `.rar` 格式，解压获取 `classes.dex` 文件
2. 然后将其复制到 `dex2jar.bat` 所在的目录下
3. 执行 `dex2jar.bat classes.dex` 获得 `classes-dex2jar.jar` 文件，运行 `jd-gui.exe` 查看 `.jar` 文件

### jadx

直接得到 `.java` 源代码

进入 bin 目录下执行 `jadx-gui.bat` 进入 GUI 界面，打开将要反编译的 APK 即可

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

TWRP 是一个由 Team Win 团队开发的第三方恢复环境，允许用户在 Android 设备的恢复模式下执行高级操作，取代了设备出厂时自带的官方恢复模式，提供更强大的功能，特别适合刷机、Root、备份和恢复等操作

---

Pixel 刷机步骤

1. 镜像下载后解压，<https://developers.google.cn/android/images?hl=zh-cn#instructions>

![20240720113516](https://image.zuoright.com/20240720113516.png)

2. 进入 fastboot 模式：`adb reboot bootloader`，同时按住「电源键 & 音量减」也可进入 bootloader 界面
3. 解锁 bootloader

![20240720113342](https://image.zuoright.com/20240720113342.png)

4. 开始刷机：`flash-all.bat`，刷完后可能提示是否清除 data 目录，选择 yes，成功后查看系统设置的版本号确认是否正确
