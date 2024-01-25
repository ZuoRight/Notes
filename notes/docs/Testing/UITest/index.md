# 引言

## JSON Wire Protocol

<https://www.selenium.dev/documentation/legacy/json_wire_protocol/>

mjwp：<https://www.cnblogs.com/nbkhic/p/3752941.html>

客户端即编写的脚本，发送请求到服务端，然后服务端会通过浏览器驱动（比如chromedriver，浏览器提供的一些api接口）与浏览器进行交互。

客户端程序库以各自的方式发起与服务器的会话：请求中包含一个被称作「预期能力（Desired Capabilities）」的 JSON 对象，这时服务器就会开启这个自动化会话，并返回一个用于发送后续命令的会话 ID。

C/S架构的好处就是server端可以不在本地，缺点是速度慢

JSON Wire 协议是 [W3C WebDriver](https://w3c.github.io/webdriver/) 的前身

## POM

Page Objects Model，Page Objects 是 Web 应用程序 UI 的简单抽象，是自动化页面测试中流行的一种设计模式，最早由 [Martin Fowler](https://martinfowler.com/bliki/PageObject.html) 提出，被 [Selenium](https://www.selenium.dev/documentation/zh-cn/guidelines_and_recommendations/page_object_models/) 所接受并推广。

<https://www.selenium.dev/documentation/test_practices/encouraged/page_object_models/>

### 目的

页面分层，松耦合

### 原则

可以将页面提供的服务或组件封装到公共方法中，但没必要封装整个页面，断言是测试的一部分，应始终在测试代码中，而不是在PO中。实例化PO时，建议判断一下页面的关键元素是否已正确加载。

- `app/`: 存放appium初始化相关的配置
- `web/`: 存放selenium初始化相关的配置
- `base/`：存放selenium和appium最基本的方法，被继承使用
    - driver 复用，避免重复调用
    - 局部导入，避免循环引用
    - 链式调用，调用自己时 return self

- 公共方法代表页面提供的服务
- 尽量不要暴露页面的内部结构
- 一般不做断言
- 方法返回其他 PageObjects
- 不需要代表整个页面
- 同一操作的不同结果被建模为不同的方法


## 测试实践

```python
user = user_factory.create_common_user()  #This method is defined elsewhere.
account_page = login_as(user.get_email(), user.get_password())
```
