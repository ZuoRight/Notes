# Selenium

SE三剑客
webdriver 网络驱动器
IDE 浏览器插件
Grid 可以做分布式测试

## 配置环境变量

如果不配置环境变量需要指定路径
driver = webdriver.Chrome(executable_path='xxx/chromedriver')

## [下载](https://www.selenium.dev/documentation/zh-cn/webdriver/driver_requirements/)

- [Chrome: chromedriver](https://chromedriver.storage.googleapis.com/index.html)
- [FireFox: geckodriver](https://github.com/mozilla/geckodriver/releases)
- Safari: safaridriver，自带(`usr/bin/`)

### Mac

1. 打开Finder，使用快捷键`Command+Shift+G`，前往文件夹`usr/local/bin`，将下载的驱动文件拖入。
2. 打开终端，执行`vim ~/.bash_profile`，添加环境变量`export PATH=$PATH:/usr/local/bin/驱动名`，保存后执行`source ~/.bash_profile`生效。
3. 执行`驱动名 --version`，如果返回版本号则说明配置正确

Tips：启动Safari，需要勾选浏览器-工具栏-开发-允许远程自动化的选项。（如果没找到开发菜单，需要在Safari浏览器-偏好设置-高级下，勾选“在菜单中显示开发菜单”）

### Windows

## WebDriver类

[webdriver api](https://www.selenium.dev/selenium/docs/api/py/api.html)

`from selenium import webdriver`

```python
driver = webdriver.Chrome

.ChromeOptions
.Firefox
.FirefoxProfile
.Ie
.Opera
.PhantomJS
.Remote
.DesiredCapabilities
.ActionChains
.TouchActions
.Proxy
```

### 属性

```python
driver.name  #浏览器名称
driver.curent_url  # 当前url
driver.title  # 当前页面标题
driver.page_source  # 当前页面源码
driver.window_handles  # 当前窗口所有句柄
driver.current_window_handle  # 当前窗口句柄
```

### 方法

```python
driver.forward()  # 前进
driver.back()  # 后退

driver.refresh()  # 刷新页面
driver.close()  # 关闭页面
driver.quit()  # 退出浏览器

driver.switch_to.alert()  # 切换到alert
driver.switch_to.alert.accept()

driver.switch_to.frame("frameName")  # 切换到frame
driver.switch_to.window(driver.window_handles[1])  # 切换标签页
driver.switch_to.active_element  # 切换到活动元素

driver.get_cookies()
```

```python
# 定位单个元素
find_element_by_xxx
# 定位多个元素
find_elements_by_xxx
```

定位到元素后会返回一个WebElement对象，用来描述一个元素

## WebElement类

### 属性

```python
e.id  # 标示
e.size  # 宽高
e.rect  # 宽高和坐标
e.text  # 文本内容
e.tag_name  # 标签名
```

### 方法

```python
e.send_keys()  # 输入内容
e.clear()  # 清空输入内容
e.click()  # 单击
e.is_selected  # 是否被选中
e.is_enabled  # 是否可用
e.is_displayed  # 是否显示
e.value_of_css_property  # css属性值

e.get_attribute("属性") # 获取属性值
e.get_attribute("class")  # 获取类
e.get_attribute("value")  # 获取输入值

# 定位到父元素后还可以继续定位子元素
e.find_element_by_xx()
```

## 下拉选项Select类

```python
from selenium import webdriver
# 需要引入Select类
from selenium.webdriver.support.select import Select

se = self.driver.find_element_by_id('s4Id')
select = Select(se)

# 通过索引勾选
select.select_by_index(3)

for i in range(7):
    select.select_by_index(i)

# 通过选项value值勾选
select.select_by_value('o4val')
# 通过选项文本勾选
select.select_by_visible_text('o1')

# 取消勾选
select.deselect_by_index(3)
select.deselect_by_value('o4val')
select.deselect_by_visible_text('o1')
# 取消所有已选选项
select.deselect_all()


# 返回所有选项
for option in select.options:
    print(option.text)

# 返回所有被选中的选项
for option in select.all_selected_options:
    print(option.text)

# 返回被选中的第一个选项
print(select.first_selected_option.text)
```


## from selenium.common.exceptions import [TheNameOfTheExceptionClass](https://www.selenium.dev/selenium/docs/api/py/common/selenium.common.exceptions.html#module-selenium.common.exceptions)


## from selenium.webdriver.common.xxx import Xxx

```text
.by 鼠标操作
.alert 弹窗操作
.keys 按键操作
.touch_actions 操作触控
.action_chains 组合操作
.utils
.proxy
.service
.desired_capabilities
.html5.application_cache
```


## from selenium.webdriver.support

```text
from selenium.webdriver.support import expected_conditions as EC
```

```text
from selenium.webdriver.support.ui import webdriverwait
```

```text
from selenium.webdriver.support.ui import Select

select = Select(driver.find_element_by_xxx)
# 不同选取方式
select.select_by_index(index)
select.select_by_visible_text("text")
select.select_by_value(value)
# 列出所有选项
select.all_selected_options
# 选取全部
select.options
# 取消全部
select.deselect_all()
```

## EC

`from selenium.webdriver.support import expected_conditions as EC`

```text
title_is: 判断当前页面的title是否精确等于预期
title_contains: 判断当前页面的title是否包含预期字符串
presence_of_element_located: 判断某个元素是否被加到了dom树里，并不代表该元素一定可见
visibility_of_element_located: 判断某个元素是否可见.可见代表元素非隐藏，并且元素的宽和高都不等于0
visibility_of: 跟上面的方法做一样的事情，只是上面的方法要传入locator，这个方法直接传定位到的element就好了
presence_of_all_elements_located: 判断是否至少有1个元素存在于dom树中。举个例子，如果页面上有n个元素的class都是‘column-md-3‘，那么只要有1个元素存在，这个方法就返回true
text_to_be_present_in_element: 判断某个元素中的text是否包含了预期的字符串
text_to_be_present_in_element_value: 判断某个元素中的value属性是否包含了预期的字符串
frame_to_be_available_and_switch_to_it: 判断该frame是否可以switch进去，如果可以的话，返回true并且switch进去，否则返回false
invisibility_of_element_located: 判断某个元素中是否不存在于dom树或不可见
element_to_be_clickable: 判断某个元素中是否可见并且是enable的，这样的话才叫clickable
staleness_of: 等某个元素从dom树中移除，注意，这个方法也是返回true或false
element_to_be_selected: 判断某个元素是否被选中了,一般用在下拉列表
element_selection_state_to_be: 判断某个元素的选中状态是否符合预期
element_located_selection_state_to_be: 跟上面的方法作用一样，只是上面的方法传入定位到的element，而这个方法传入locator
alert_is_present: 判断页面上是否存在alert
```

## 等待

### 强制等待

```text
import time
sleep(5)
```

### 隐性等待

需要注意的是，当find_element与click()连用，隐性等待会失效

```text
driver.implicitly_wait(5)
```

### 显性等待

```text
from selenium.webdriver.support.ui import WebDriverWait

webdriverwait(driver, timeout, poll_frequency=0.5, ignored_exceptions=none)
# driver 浏览器驱动
# timeout 最长超长时间
# poll_frequency 检测的间隔时间，默认0.5s
# ignored_exceptions 超时后的异常信息，默认情况下抛nosuchelementexception异常
```

后面通常接until()或until_not()方法一起使用

```text
# 直到返回true
.until(EC.method, message='')

# 直到返回false
.until_not(EC.method, message='')
```

---

element = driver.find_element_by_xxx

element.clear()
element.send_keys()
element.submit()