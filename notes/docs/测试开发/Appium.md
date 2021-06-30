# Appium

- appium server
  > [桌面版：Appium Desktop](https://github.com/appium/appium-desktop/releases)：包含 appium 服务，录制工具，查看页面布局及查找元素的功能  
  > 命令行版(更稳定)：`npm install -g appium --chromedriver-skip-install`  
  > 环境检查工具：`npm install -g appium-doctor`

- appium client
  > `pip install appium-python-client`

## 设计原则/特点

- iOS：UIAutomation
- Android 4.2+：UiAutomator
- Android 2.3+：Instrumentation(已被selendroid取代)

## Android\tools中的一些工具

[参考](http://www.cnblogs.com/nbkhic/p/3806886.html)
1. uiautomatorviewer.bat
  主要用来查看控件的属性，比如resource id，class name等
  也可查看被测app的appPackage（Desired Capabilities中使用）
2. monitor.bat
  该工具可以帮我们找到android控件的content-description，为以后的find_element_by_accessibility_id 定位方法做参数使用

## 获取android app的Activity

三种方法：
1. 源码
2. 没有源码，可以[反编译工具](http://www.cnblogs.com/nbkhic/p/3806951.html)
3. 使用log查看大法

## 编写脚本

> [appium的基本概念](http://www.cnblogs.com/nbkhic/p/3803830.html)(乙醇)  
> [Desired Capabilities详解](http://www.cnblogs.com/nbkhic/p/3805805.html)(乙醇)

## 运行

1. 连接设备/模拟器
2. 启动 Appium Server
3. 运行脚本

## 控件定位

[基础](http://www.cnblogs.com/nbkhic/p/3807871.html)

[使用resource id定位](http://www.cnblogs.com/nbkhic/p/3813792.html)
