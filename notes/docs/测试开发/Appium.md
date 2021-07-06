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

[官方示例](https://github1s.com/appium/appium/blob/master/sample-code/python/README.md)

### iOS

待补充

### Android

```python
from appium import webdriver

caps={
    "platformName": "Android",
    "appPackage": "com.android.settings",
    "appActivity": ".MainSettings"
}

driver=webdriver.Remote("http://localhost:4723/wd/hub", caps)
```

### H5

需要下载相应版本的chromedriver，caps中指定`chromedriverExecutable`:`path`

或者放到默认读取的路径下（具体可查看appium日志信息），比如我Windows系统放在`~AppData\Roaming\npm\node_modules`的`\appium\node_modules`下的`\appium-chromedriver\win`中。

```python
caps={
    "platformName": "Android",  # 平台名
    "browserName": "Chrome",  # 测试自带浏览器改为 Browser
    # 另外不需要再配置包名和页面
}

driver = webdriver.Remote("http://localhost:4723/wd/hub", caps)
driver.get("http://m.baidu.com")
```

### 混合应用

如果用真机测试，需要开发将APP的WebView调试开关打开。

混合应用caps中不需要配置`browserName`字段，跟原生应用一样即可，但是需要切换上下文。

```python
import time
from appium import webdriver
from appium.webdriver.common.mobileby import MobileBy


class TestWework:
    def setup(cls):
        caps = {
            "platformName": "Android",
            "deviceName": "time",
            "appPackage": "org.geekbang.geekTime",
            "appActivity": ".project.start.MainActivity",
            "noReset": True,
            # 提升启动app速度的配置
            "skipDeviceInitialization": True
        }

        cls.driver = webdriver.Remote("http://localhost:4723/wd/hub", caps)
        cls.driver.implicitly_wait(10)


    def teardown(cls):
        cls.driver.quit()


    def test_add(self):
        # 打开极客时间后点击我的
        self.driver.find_element(MobileBy.XPATH, "//*[@text='我的']").click()
        # 点击帮助/反馈，跳到H5页面
        self.driver.find_element(MobileBy.XPATH, "//*[@text='帮助/反馈']").click()
        time.sleep(3)
        # 注意这里：原生应用中打开的H5，需要先切换一下上下文
        webview = self.driver.contexts[-1]  # -1为最新打开的H5
        self.driver.switch_to.context(webview)
        # 然后就像定位PC端网页一样操作即可
        self.driver.find_element(MobileBy.XPATH, '//*[@id="app"]/div[1]/ul/li[1]').click()
        # 如果H5中有新的跳转，可能还需要切换windows
        time.sleep(3)
```

## 运行

1. 启动Appium服务：`appium` [[参数](https://appium.io/docs/en/writing-running-appium/server-args/index.html)]
2. 连接设备/模拟器，确保开发者选项中类似USB调试(安全)等开关为开启状态，否则可能会报代号255的错
3. 运行脚本

## [Desired Capabilities](https://appium.io/docs/en/writing-running-appium/caps/index.html)

```json
caps= {
    // 必需要有的
    "platformName" : "Android",  // 平台名称
    "appPackage" : "com.android.settings",  // 包名
    "appActivity" : "com.android.settings.Settings",  // 页面

    // 可有可无
    "automationName": "appium",  // 自动化引擎，默认appium，自动根据platformName选择合适的引擎
    "platformVersion" : "6.0",  // 平台版本
    "deviceName" : "emulator-5554",  // 设备名称，随意填
    
    // 其它常用
    "ensureWebviewsHavePages": True,
    "skipDeviceInitialization": True  // 跳过设备初始化，可以提高启动速度
}
```

## 定位元素

### 常规定位

```python
from appium.webdriver.common.mobileby import MobileBy
# XPATH 较慢，但比较灵活
loc = '//*[@resource-id="xx"]'
loc = '//*[contains(@text, "xx")]'
loc = '//*[@class="xx" and @content-desc="xx"]'

driver.find_element(MobileBy.XPATH, loc)
```

```python
# resource-id
# 如果不是很正式的名字有可能是动态id，不同设备上不一样，此时不建议使用
driver.find_element(By.ID, '包名:id/value')
```

```python
# accessibility-id 无障碍做的好的应用才有
driver.find_element_by_accessibility_id('xxx')
```

### [UiAutomator API定位](https://developer.android.google.cn/reference/android/support/test/uiautomator/package-summary)

1.9版本后新增了css selector方式自动转换为UiAutomator API

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
driver.find_element(MobileBy.XPATH, toast_xpath)
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
