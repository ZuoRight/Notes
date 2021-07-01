# Appium

- appium server
  > [桌面版：Appium Desktop](https://github.com/appium/appium-desktop/releases)：包含 appium 服务，录制工具，查看页面布局及查找元素的功能
  >
  > 命令行版(更稳定)：`npm install -g appium --chromedriver-skip-install`  
  > 环境检查工具：`npm install -g appium-doctor`

- appium client
  > `pip install appium-python-client`

## Demo

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

1. 启动Appium服务
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
