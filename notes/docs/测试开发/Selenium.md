# Selenium

- [官网](https://www.selenium.dev/)
- [API文档](https://www.selenium.dev/selenium/docs/api/py/api.html)（[中文](https://selenium-python-zh.readthedocs.io/en/latest/)）

用于测试的demo网站：<https://sahitest.com/demo/>

## 原理

由以下三部分组成

- **Webdriver** 网络驱动器
- **IDE** 浏览器插件
- **Grid** 可以做分布式测试

## 环境搭建

1. 安装Selenium插件：`pip install selenium`
2. [下载浏览器驱动](https://www.selenium.dev/documentation/zh-cn/webdriver/driver_requirements/)
    - [Chrome: chromedriver](https://chromedriver.storage.googleapis.com/index.html)
    - [FireFox: geckodriver](https://github.com/mozilla/geckodriver/releases)
    - Safari: safaridriver(`usr/bin/`中已存在，无需再下载)

    > 注意：需要下载与本机浏览器相对应版本的驱动，否则运行时会报错。

3. 配置环境变量

### Mac

1. 打开Finder，使用快捷键`Command+Shift+G`，前往文件夹`usr/local/bin`，将下载的驱动文件拖入。
2. 打开终端，执行`vim ~/.bash_profile`，添加环境变量`export PATH=$PATH:/usr/local/bin/驱动名`，保存后执行`source ~/.bash_profile`生效。
3. 执行`驱动名 --version`，如果返回版本号则说明配置正确，如果提示无法验证开发者，需要到`系统偏好设置/安全与隐私/通用`界面准许。

### Windows

1. 下载的驱动文件可以集中放在一个文件夹内，比如`selenium-driver`
2. 然后将该文件路径添加到`环境变量\系统变量\Path`中
3. 打开终端，执行`驱动名 --version`，如果返回版本号则说明配置正确

## 启动浏览器

```python
from selenium import webdriver

# 启动Chrome
driver = webdriver.Chrome()
# 启动Firefox
driver = webdriver.Firefox()
# 启动Safari
# 注意：需要先在【Safari浏览器-偏好设置-高级】，勾选【“在菜单中显示开发”】，然后在【开发】，勾选【允许远程自动化】
driver = webdriver.Safari()

# 如果没有配置环境变量，需要指定路径
driver = webdriver.Chrome(executable_path='Xxx/chromedriver')
```

## 操控浏览器·WebDriver

### 属性

```python
print(driver.name)  #浏览器名称
print(driver.curent_url)  # 当前url
print(driver.title)  # 当前页面标题
print(driver.page_source)  # 当前页面源码
print(driver.window_handles)  # 当前窗口所有句柄
print(driver.current_window_handle)  # 当前窗口句柄
```

### 方法

```python
driver.forward()  # 前进
driver.back()  # 后退

driver.refresh()  # 刷新页面

driver.close()  # 关闭页面
driver.quit()  # 退出浏览器

driver.get_cookies()
```

```python
driver.switch_to.frame(element)  # 切换到frame
driver.switch_to.default_content()  # 返回主frame
driver.switch_to.parent_frame()  # 返回父frame

driver.switch_to.window(driver.window_handles[1])  # 切换标签页

driver.switch_to.active_element  # 切换到活动元素
```

```python
# 执行js脚本
driver.execute_script(js)  # 同步
driver.execute_async_script(js)  # 异步
```

## 定位元素

```python
# 定位单个元素
e = driver.find_element_by_xxx
# 定位多个元素
e = driver.find_elements_by_xxx

# 通过By类定位
from selenium.webdriver.common.by import By
e = driver.find_element(By.ID, "")
```

定位到元素后会返回一个WebElement对象，用来描述一个元素

## 操控元素·WebElement

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
e.send_keys("xxx")  # 输入内容
e.send_keys(Keys.CONTROL, "v")  # 还可以输入快捷键

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

## 弹框的处理

弹框主要有三种，处理方式基本一致

- alert
- confirm
- prompt

```python
# 先定位到弹框
# 然后切换到alert
a = driver.switch_to.alert

a.accept()  # 确认
a.dismiss()  # 取消
a.send_keys("xxx")  # 输入内容
a.text  # 显示弹框的文本
```

## 下拉框的处理

```python
from selenium import webdriver
# 需要引入Select类
from selenium.webdriver.support.select import Select

se = self.driver.find_element_by_id('s4Id')
select = Select(se)
```

```python
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

## 等待的处理

隐性等待和显性等待同时存在时，取两者中最长的等待时间为有效等待时间。

### 强制等待

强制线程休眠一定时间，一般只在调试过程中使用

```python
import time
sleep(5)
```

### 隐性等待

在指定时间内，自动每隔0.5s查找一次元素，找到则退出循环，没找到则继续，超时则抛出异常。

```python
driver.implicitly_wait(5)
```

需要注意的是，当定位到元素后连用`.click()`时，隐性等待会失效

### 显性等待

```python
from selenium.webdriver.support.ui import WebDriverWait

# driver
# timeout 超时时间
# poll_frequency 检测频率，默认为0.5s
# ignored_exceptions 超时后抛出异常，默认为NoSuchElementException
wait = WebDriverWait(self.driver, timeout, poll_frequency, ignored_exceptions=None)

# 满足预期条件时停止
wait.until(method, message='')
# 不满足预期条件时停止
wait.until_not(method, message='')
# 超时则抛出TimeoutException`、异常
```

其中的method可以自定义也可以使用EC模块提供的函数


## 预期条件·EC

貌似必须得配合显性等待使用

`from selenium.webdriver.support import expected_conditions as EC`

```python
# 判断页面上是否存在弹框
EC.alert_is_present()

# 判断该frame是否可以switch进去，如果可以的话，返回true并且switch进去，否则返回false
EC.frame_to_be_available_and_switch_to_it()
```

```python
# 判断title是否为xxx
EC.title_is("xxx")
# 判断title是否包含xxx
EC.title_contains("xxx")

# 判断某元素的文本是否包含xxx
EC.text_to_be_present_in_element(locator, xxx)
# 判断某元素的属性值是否包含xxx
EC.text_to_be_present_in_element_value(locator, xxx)
```

```python
# 判断某个元素中是否存在于dom或不可见
EC.invisibility_of_element_located(locator)
# 判断等待某个元素从dom树中是否移除
EC.staleness_of()

# 判断某个元素是否被加到了dom里，但不一定可见
EC.presence_of_element_located(locator)
# 判断是否至少有1个元素存在于dom中
EC.presence_of_all_elements_located(locator)
```

```python
# 判断元素是否可见，可见返回这个元素
EC.visibility_of(element)
# 判断某个元素是否可见，宽和高等大于0
EC.visibility_of_element_located(locator)
# 判断是否至少有一个元素可见
EC.visibility_of_any_elements_located(locator)
# 判断某个元素中是否可见并且是可点击的
EC.element_to_be_clickable(locator)
```

```python
# 判断某个元素是否被选中了,一般用在下拉列表中
EC.element_to_be_selected(element)
# 判断某个元素的选中状态是否符合预期
EC.element_selection_state_to_be(element)
# 判断某个元素的选中状态是否符合预期
EC.element_located_selection_state_to_be(locator, is_selected)
```

## ActionChains

调用ActionsChains方法时，不会立即执行，而是将所有操作按顺序存放在一个队列中，当调用perform()方法时，队列中的事件会依次执行。

```python
from selenium.webdriver import ActionChains

action = ActionChains(driver)  # 生成一个动作
action.添加方法1
action.添加方法2
action.perform()  # 执行


```

### 鼠标操作

```python
from selenium.webdriver import ActionChains

ActionChains(self.driver).func1().func2().perform()  # 按顺序执行func1和func2

.reset_actions()  # 清除已存储的actions
.pause(seconds)  # 保持n秒
```

```python
.click()  # 单击左键
.context_click()  # 单击右键
.double_click()  # 双击左键

.click_and_hold()  # 单击左键，不松开
.release()  # 在某个元素上松开按键

.drag_and_drop(source, target)  # 拖拽到某个元素后松开
.drag_and_drop_by_offset(source, xoffset, yoffset)  # 拖拽到某个坐标后松开

.move_to_element(to_element)  # 鼠标移到某个元素hover
.move_by_offset(xoffset, yoffset)  # 鼠标移到某个坐标hover
.move_to_element_with_offset(to_element, xoffset, yoffset)  # 鼠标移动到距某个元素一定距离的位置hover
```

### 按键操作

```python
# Actions类中的sendKeys
from selenium.webdriver.common.keys import Keys

element = driver.find_element_by_xxx
element.click()

# 注意：执行完perform后面如果再想执行其它actions，需要重新实例化actions
actions = ActionChains(self.driver)

actions.send_keys(Keys.SPACE)
actions.send_keys_to_element(element, Keys.xxx)

actions.key_down(Keys.CONTROL).send_keys("a").key_up(Keys.CONTROL)

actions.perform()
```

还可以

```python
# WebElement.sendKeys
e.send_keys(Keys.CONTROL, "a")
```

## 截图

```python
driver.save_screenshot('xxx.png')  # 保存截图
driver.get_screenshot_as_file(file_path)  # 获取当前截图的完整路径
driver.get_screenshot_as_base64()  # 获取当前截图的base64字符串
driver.get_screenshot_as_png()  # 获取当前截图的二进制数据
```


## 异常

`from selenium.common.exceptions import TimeoutException`

## Chrome复用调试

1. 关闭所有Chrome浏览器窗口，并保证进程已退出
2. 命令行以调试模式启动Chrome，端口可自定义
   - windows：`path\chrome --remote-debugging-port=9222`
   - mac：`Google\ Chrome --remote-debugging-port=9222`
3. 访问<http://127.0.0.1:9222/>查看是否成功
3. 使用如下脚本即可在当前窗口直接操作
   ```python
    def test_debug():
        opt = webdriver.ChromeOptions()
        opt.debugger_address = "localhost:9222"
        driver = webdriver.Chrome(options=opt)
        driver.get("xxx")
   ```