# POM

Page Objects Model，Page Objects 是 Web 应用程序 UI 的简单抽象，是自动化页面测试中流行的一种设计模式，最早由 [Martin Fowler](https://martinfowler.com/bliki/PageObject.html) 提出，被 [Selenium](https://www.selenium.dev/documentation/zh-cn/guidelines_and_recommendations/page_object_models/) 所接受并推广。

<https://www.selenium.dev/documentation/test_practices/encouraged/page_object_models/>

目的：页面分层，松耦合

## 原则

- 测试用例中尽量不要暴露页面的内部结构，可以将页面提供的服务或组件封装到公共方法中，比如 xxx_page，但没必要封装整个页面，也可以是某个功能模块

```text
po/
    base/：存放selenium和appium最基本的方法，其它页面继承base复用__init__()，避免重复初始化
    app/: 存放appium初始化相关的配置
    web/: 存放selenium初始化相关的配置
```

- xxx_page 中同一操作的不同结果最好建模为不同的方法
- 实例化页面对象时，最好先判断一下页面的关键元素是否已正确加载
- 断言是测试的一部分，应始终在测试代码中，即 xxx_page 中一般不做断言
- xxx_page 中返回 yyy_page，形成链式调用，使得逻辑跳转清晰，另外最好使用局部导入，避免循环引用

```python
# 调用其它页面
def goto other_page(self):
    return OtherPage(self.driver)

# 调用自己
def goto_self(self):
    return self
```

## 测试实践

### `base_page.py`

```python
import os

from selenium import webdriver
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC


class BasePage:
    _browser = os.getenv("browser", "chrome")
    _url = ""  # 通常是index页面

    def __init__(self, driver_base=None):
        if driver_base:
            # 链式调用时，复用self.driver，不重复初始化
            self.driver = driver_base
        else:
            if self._browser == "safari":
                self.driver = webdriver.Safari()
            else:
                options = webdriver.ChromeOptions()
                # 去除Chrome正在受到自动软件的控制的提示
                options.add_experimental_option('useAutomationExtension', False)
                options.add_experimental_option('excludeSwitches', ['enable-automation'])
                # 关闭是否保存密码弹框
                prefs = {"credentials_enable_service": False, "profile.password_manager_enabled": False}
                options.add_experimental_option("prefs", prefs)
                # 仅加载完DOM后即可开始定位
                options.page_load_strategy = 'eager'
                # 运行完不自动关闭窗口
                # options.add_experimental_option("detach", True)
                self.driver = webdriver.Chrome(options=options)
            self.driver.maximize_window()
            self.driver.get(self._url)


    def find_input(self, locator):
        element = WebDriverWait(self.driver, 5).until(
            EC.presence_of_element_located(locator)
        )
        return element


    def find_button(self, locator):
        element = WebDriverWait(self.driver, 5).until(
            EC.element_to_be_clickable(locator)
        )
        return element


    def find_select(self, locator):
        pass        
```

### `login_page.py`

```python
import time
from selenium.webdriver.common.by import By
from po.base_page import BasePage


class LoginPage(BasePage):
    _agree_button = (By.XPATH, "")
    _login_button = (By.XPATH, "")
    _email_input = (By.CSS_SELECTOR, "")
    _password_input = (By.CSS_SELECTOR, "")
    

    def login(self, url, email, password):
        # 打开登陆页面
        self.driver.get(url)

        # 点击我同意
        agree_button = self.find_button(self._agree_button)
        agree_button.click()

        # 输入邮箱
        email_input = self.find_input(self._email_input)
        email_input.send_keys(email)

        # 输入密码
        password_input = self.find_input(self._password_input)
        password_input.send_keys(password)

        # 点击登录
        login_button = self.find_button(self._login_button)
        login_button.click()
        time.sleep(3)  # 强制3s等待页面加载


    def logout(self):
        pass


    # 跳转其它页面
    def goto_kyc_page(self):
        from po.kyc_page import KYCPage  # 局部引用
        return KYCPage(self.driver)  # 复用driver
```

登陆如果不是被测试对象，最好不要使用UI自动化的方式实现，而是采用接口登陆，然后设置浏览器Cookies或Token保持登陆，减少页面加载和等待时间

```python
# 接口登陆
response = requests.post(login_url, data=login_data)
cookies = response.cookies  # <RequestsCookieJar[<Cookie csrftoken=4NKYzMJ9...b8MzTTJfwF3n for example.com/>, <Cookie sessionid=bq6r4ycioahmt...d2di0srnv1 for example.com/>]>
token = response.json().get('token')
```

- 设置 Cookies

```python
driver.get('https://example.com')  # 打开网站，确保URL匹配Cookies的域
# 需要将Cookeis对象转换为Dict格式
cookies_dict = {
    'name': cookie.name,
    'value': cookie.value,
    'path': '/',
    'domain': cookie.domain
}
for cookie in cookies:
    driver.add_cookie(cookies_dict)
driver.get('https://example.com')  # 再次访问网站，以便使用新的登录状态
```

- 设置 Token

Token 通常是存储在 Local Storage 或 Session Storage 或设置在请求头中

```python
# localStorage和sessionStorage是按照同源策略进行隔离的，这意味着只能在设置了这些数据的相同域名下访问
# 在设置token前访问一次，确保JavaScript代码在正确的域上下文中运行它们
driver.get('https://example.com')

# 通过 JavaScript 在 Local Storage 中设置 token
driver.execute_script("window.localStorage.setItem('token', '{}');".format(token))
# 或者设置到 Session Storage
driver.execute_script("window.sessionStorage.setItem('token', '{}');".format(token))


# 刷新或导航，以便使用新的登录状态
driver.get('https://example.com')
```

- 修改请求头

Selenium WebDriver API 本身不支持直接修改 HTTP 请求头，如果必须要在请求头中携带 token 或其它字段，可以考虑使用代理服务器（如BrowserMob Proxy）来拦截和修改发往服务器的HTTP请求。大致步骤：下载并启动 BrowserMob Proxy，配置 Selenium 使用 BrowserMob Proxy 作为 HTTP 代理，使用 BrowserMob Proxy 的 API 来添加或修改请求头。

### `xxx_test.py`

```python
import pytest

type_list = ["id", "passport"]

class TestKYC:
    def setup_class(cls):
        user_dict = {"email": "test@demo.com", "password": "xxx"}
        from po.login_page import LoginPage
        cls.login_page = LoginPage()
        # self.login_page.login(user_dict)
        cls.login_page.login_by_api(user_dict)

    @pytest.mark.parametrize("type", type_list)
    def test_submit_kyc(self, type):
        kyc_page = self.login_page.goto_kyc_page()
        kyc_page.fill_document(type)
        kyc_page.fill_profile()
        kyc_page.fill_address()
        assert True
```

如果测试不同type需要登陆不同的email，可以使用 setup + fixture 的方式

```python
import pytest


test_data = [
    ('test1@demo.com', 'id'),
    ("test2@demo.com", 'passport')
]


class TestKYC:
    # 注意，scope范围要用function
    @pytest.fixture(scope="function", params=test_data, autouse=True)
    def setup_class(self, request):
        email, document_type = request.param
        user_dict = {"email": email, "password": "xxx"}
        from po.login_page import LoginPage
        self.login_page = LoginPage()
        self.login_page.login_by_api(user_dict)
        return document_type

    def test_submit_kyc(self, setup_class):
        document_type = setup_class
        kyc_page = self.login_page.goto_kyc_page()
        kyc_page.fill_document(document_type)
        kyc_page.fill_profile()
        kyc_page.fill_address()
        assert True
```
