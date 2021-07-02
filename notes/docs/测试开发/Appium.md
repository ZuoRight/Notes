# Appium

API文档：<https://appium.io/docs/en/about-appium/api/>

基于Webdriver做了一些扩展，调用各平台自带的测试框架，测哪个平台就安哪个平台的工具包

- 测Android，依赖 [UiAutomator2](https://appium.io/docs/en/drivers/android-uiautomator2/)，就需要 Android SDK
- 测iOS真机，依赖[XCUITest](https://appium.io/docs/en/drivers/ios-xcuitest-real-devices/)，需要XCode

## 环境搭建

- appium server
  > [桌面版：Appium Desktop](https://github.com/appium/appium-desktop/releases)：包含 appium 服务，录制工具，查看页面布局及查找元素的功能
  >
  > 命令行版(更稳定)：`npm install -g appium --chromedriver-skip-install`  
  > 环境检查工具：`npm install -g appium-doctor`

- [appium client](https://github.com/appium/python-client)
  > `pip install appium-python-client`

## Demo

iOS

Android

```python
from appium import webdriver

# Android至少要有的三个配置字段，其它视情况可选
caps={
    "platformName" : "Android",  # 平台名
    "appPackage" : "com.android.settings",  # 包名
    "appActivity" : "com.android.settings.Settings",  # 页面
}

driver = webdriver.Remote("http://localhost:4723/wd/hub", caps)
driver.implicitly_wait(5)

el1 = driver.find_element_by_accessibility_id("Search settings")
el1.click()
el2 = driver.find_element_by_id("android:id/search_src_text")
el2.send_keys("More")

driver.quit()
```

运行

1. 启动Appium服务：`appium` [[参数](https://appium.io/docs/en/writing-running-appium/server-args/index.html)]
2. 连接设备/模拟器
3. 运行脚本

[官方示例](https://github1s.com/appium/appium/blob/master/sample-code/python/README.md)

常用[Desired Capabilities](https://appium.io/docs/en/writing-running-appium/caps/index.html)

```python
"platformName" : "Android",  # 平台名称
"platformVersion" : "6.0",  # 平台版本
"automationName": "appium"  # 自动化引擎
"deviceName" : "emulator-5554",  # 设备名称
"appPackage" : "com.android.settings",  # 包名
"appActivity" : "com.android.settings.Settings",  # Activity
"ensureWebviewsHavePages": True
app  # apk路径
```

## 教程

[appium的基本概念](http://www.cnblogs.com/nbkhic/p/3803830.html)(乙醇)

[Desired Capabilities详解](http://www.cnblogs.com/nbkhic/p/3805805.html)(乙醇)

[基础](http://www.cnblogs.com/nbkhic/p/3807871.html)

[使用resource id定位](http://www.cnblogs.com/nbkhic/p/3813792.html)

## 查看布局结构和组件属性

- uiautomatorviewer

Android ADT 自带，但需要基于jdk1.8版本

- appium desktop

Appium Desktop 自带了一个 inspector，需要先配置一些信息

- weditor

阿里openatx团队开发的

安装：`pip install -U weditor`
运行：`weditor`

有线连接：设备连接电脑，输入设备序列号

无线连接：`adb tcpip 5555`，输入ip:端口

## 获取包名和Activity
