# POM

Page Object Model，是自动化页面测试(AUI)中流行的一种设计模式，最早由[Martin Fowler](https://martinfowler.com/bliki/PageObject.html)提出，被[Selenium](https://www.selenium.dev/documentation/zh-cn/guidelines_and_recommendations/page_object_models/)所接受并推广。

目的：页面分层，松耦合

原则：

可以将页面提供的服务或组件封装到公共方法中，但没必要封装整个页面，断言是测试的一部分，应始终在测试代码中，而不是在PO中。实例化PO时，建议判断一下页面的关键元素是否已正确加载。

app 存放appium初始化相关的配置

web 存放selenium初始化相关的配置

Base：存放selenium和appium最基本的方法

继承Base

driver复用，避免重复调用

局部导入，避免循环引用

链式调用，调用自己时return self
