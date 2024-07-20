# 日志分析

<https://blog.csdn.net/wxlinwzl/article/details/7011755>

使用 DDMS 查看日志

- 时间信息
- 系统信息：内存，CPU，进程队列，虚拟内存，垃圾回收等
- 虚拟机信息：进程和线程的跟踪信息

```shell
/data/anr  # 保存发生 ANR Crash 时的相关信息
/data/dontpanic/  # 保存发生 Kernel Panic 时的相关信息
/data/tombstones/  # 保存发生 Tombstone Crash 时的错误信息
/data/data/com.android.shell/files/bugreports  # 保存发生异常时的相关系统信息，也可以通过 adb shell bugreport 命令提取
MonkeyScreenLog.Log  # 保存 Monkey 测试过程、应用层错误信息，发生 Native Crash 时，在此文件也会有记录
```

## 用户日志上报

应用程序出现问题时，采用 HTTP 的方式将日志及时上报到后台进行分析

- 方法 1

网上大部分是采用日志收集的第三方 jar 包来完成的，还有一种是自定义一个自己的 CrashHandler 实现 UncaughtExceptionHandler 接口，来捕获闪退信息然后上传到自己的服务器

如果用户误删了闪退的日志文件，那么就会导致无法及时上报闪退日志，也就无从分析隐藏的 Bug 了

- 方法 2

在程序发生异常时提醒用户发生了什么样的异常，同时把本次捕获的 Exception 的字段写入到自己定义的 log 文件中，然后上报异常字段到自己的异常服务器上

从手机端或者后台都可以看到发生的异常堆栈，日志记录系统不借助与任何第三方 jar 包

## 异常 Exception

<https://blog.csdn.net/zhangteng22/article/details/52947992>

### Java 层

Java 代码导致 JVM 退出，弹出「程序已经崩溃」的对话框，最终用户点击关闭后进程退出

Logcat 会在「AndroidRuntime」标签下输出 Java 的调用栈

崩溃堆栈：`java.lang.xxxException`, `android.view.xxxException`

<https://blog.csdn.net/founder_chenhao/article/details/50171983>

主要分框架层和应用层（FC 和 ANR）

- FC, Force Closed 强制关闭

关键字：`FATAL EXCEPTION`(致命)

原因：空指针或未捕捉的异常

- ANR, Application No Response 无响应

<https://blog.csdn.net/dadoneo/article/details/8270107>

原因：死锁、系统反应迟钝、CPU 负载过重、应用主线程卡住，对其他请求响应超时

```text
keyDispatchTimeout, Activity 5s无响应
BroadcaseTimeout, Receiver 10s无响应
ServiceTimeout, Service 20s无响应
```

### NDK 层

NDK, Native Development Kit

Logcat 会在「debug」标签下输出 dump 信息

Tombstone Crash

一般与 so 相关

### Kernel 层

Kernel Panic

## Logcat

```shell
# 获取日志
adb logcat  # 格式：日志时间，进程号，线程号，级别，TAG，内容

adb logcat -c  # 清除旧日志
adb logcat -v <format>  # 格式化输出log
adb logcat -f <filename>  # 输出log到指定文件
adb logcat -b <buffer>  # 打印指定 buffer 的日志信息
'
main：主log区，默认
events：事件相关的log
radio：射频，telephony相关的log
'

# 使用 grep 过滤关键字信息，加 -i 忽略大小写，使用正则 "^..Activity"
# 获取包名和 Activity
adb logcat | grep START
'
在吊起应用时会显示 START：cmp=包名
所以打印带有 START 关键字的日志就可以找到当前吊起的应用的包名
'

# 获取入口
adb logcat| grep -i displayed
aapt dump badging mobike.apk | grep launchable-activity
apkanalyzer
```

- bugreport

收集 dumpsys、dumpstate、logcat 等数据，用于后续分析，比如耗电量

```shell
adb bugreport > path/bugreport.log
```
