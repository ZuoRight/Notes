# Appium

API文档：<https://appium.io/docs/en/about-appium/api/>

基于Webdriver做了一些扩展，调用各平台自带的测试框架，测哪个平台就安哪个平台的工具包

- 测Android，依赖 [UiAutomator2](https://appium.io/docs/en/drivers/android-uiautomator2/)，就需要 Android SDK
- 测iOS真机，依赖[XCUITest](https://appium.io/docs/en/drivers/ios-xcuitest-real-devices/)，需要XCode

客户端程序库以各自的方式发起与服务器的会话：请求中包含一个被称作「预期能力（Desired Capabilities）」的 JSON 对象，这时服务器就会开启这个自动化会话，并返回一个用于发送后续命令的会话 ID。

## 环境搭建

- appium server
  > [桌面版：Appium Desktop](https://github.com/appium/appium-desktop/releases)：包含 appium 服务，录制工具，查看页面布局及查找元素的功能
  >
  > 命令行版(更稳定)：`npm install -g appium --chromedriver-skip-install`  
  > 环境检查工具：`npm install -g appium-doctor`

- [appium client](https://github.com/appium/python-client)
  > `pip install appium-python-client`

## [Desired Capabilities](https://appium.io/docs/en/writing-running-appium/caps/index.html)

- platformName：要自动化的平台名称
- platformVersion：要自动化的平台版本
- deviceName: 自动化设备的种类
- app：您想要自动化的应用程序的路径（但browserName 在自动化 Web 浏览器的情况下使用该功能）
- automationName：您要使用的驱动程序的名称

## Demo

[官方示例](https://github1s.com/appium/appium/blob/master/sample-code/python/README.md)

```python
from appium import webdriver

caps={
    "platformName" : "Android",
    "platformVersion" : "6.0",
    "deviceName" : "emulator-5554",
    "appPackage" : "com.android.settings",
    "appActivity" : "com.android.settings.Settings",
    "ensureWebviewsHavePages": True
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

## 获取Activity

1. 源码
2. 没有源码，可以[反编译工具](http://www.cnblogs.com/nbkhic/p/3806951.html)
3. 使用log查看大法

## 教程

[appium的基本概念](http://www.cnblogs.com/nbkhic/p/3803830.html)(乙醇)

[Desired Capabilities详解](http://www.cnblogs.com/nbkhic/p/3805805.html)(乙醇)

[基础](http://www.cnblogs.com/nbkhic/p/3807871.html)

[使用resource id定位](http://www.cnblogs.com/nbkhic/p/3813792.html)
