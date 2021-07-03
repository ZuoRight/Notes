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
2. 连接设备/模拟器，确保开发者选项中类似USB调试(安全)等开关为开启状态，否则可能会报代号255的错
3. 运行脚本

[官方示例](https://github1s.com/appium/appium/blob/master/sample-code/python/README.md)

常用[Desired Capabilities](https://appium.io/docs/en/writing-running-appium/caps/index.html)

```python
"platformName" : "Android",  # 平台名称
"platformVersion" : "6.0",  # 平台版本
"automationName": "appium"  # 自动化引擎
"deviceName" : "emulator-5554",  # 设备名称，随意
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

## DOM分析

- uiautomatorviewer
  > Android ADT 自带，但需要基于jdk1.8版本

- appium desktop
  > Appium Desktop 自带了一个 inspector，需要先配置一些信息

- weditor
  > 阿里openatx团队开发的
  >
  > - 安装：`pip install -U weditor`
  > - 运行：`weditor`
  > - 连接：输入设备序列号或ip:5555（需要先开启）

## 定位元素

### 常规定位

```python
# resource-id
driver.find_element(By.ID, '包名:id/value')
```

```python
# accessibility-id
driver.find_element_by_accessibility_id('xxx')
```

```python
from appium.webdriver.common.mobileby import MobileBy
# XPATH
loc = '//*[@resource-id="xx"]'
loc = '//*[contains(@text, "xx")]'
loc = '//*[@class="xx" and @content-desc="xx"]'

driver.find_element(MobileBy.XPATH, loc)
```

### [UiAutomator API定位](https://developer.android.google.cn/reference/android/support/test/uiautomator/package-summary)

- UiSelector

```python
code = 'new UiSelector().text("文本")'
code = 'new UiSelector().textContains("文本")'
code = 'new UiSelector().textStartsWith("文本")'
code = 'new UiSelector().text("文本")'

code = 'new UiSelector().resourcedId("xxxidxxx")'
code = 'new UiSelector().resourcedId("xxxidxxx").text("文本")'  # 组合

code = 'new UiSelector().className("xxx.xx.xxx")'  # 通常有多个结果，然后结果下标过滤

code = 'new UiSelector().description("content-desc 文本")'

driver.find_element_by_android_uiautomator(code)
```

- UiScrollable

```python
code = 'new UiScrollable(
            new UiSelector().scrollable(true).instance(0)  # .scrollable(true) 查找匹配到的第一个可滚动组件
        )
        .scrollIntoView(  # .scrollIntoView 滚动并查找textview(默认滚动30次)
            new UiSelector().text("文本").instance(0)
        );'
driver.find_element(MobileBy.ANDROID_UIAUTOMATOR, code)
```

### 定位toast

```python
toast_xpath = "//*[@class='android.widget.Toast']"
driver.find_element(MoBileBy.XPATH, toast_xpath)
```

### 定位到元素后获取属性

```python
ele.get_attribute("clickable")  # 是否可点击
ele.get_attribute("checked")  # 是否可选中
ele.get_attribute("displayed")  # 是否可见
ele.get_attribute("enabled")  # 是否可用
```

### 获取页面源码

```python
driver.page_source  # 获取页面代码，appium是XML，selenium是HTML
```

## H5页面

driver.switch_to.context()

