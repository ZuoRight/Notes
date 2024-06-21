# 引言

## W3C标准

万维网（World Wide Web）是由很多链接在一起的文档和资源构成的，为此万维网联盟制定了一系列标准

- 结构标准语言：XML、HTML
- 行为标准语言：DOM、ECMAScript
- 表现标准语言：CSS

## 架构

- `MVC`: Modle-View-Controller 经典 Web 应用架构
- `MVP`: Model-View-Presenter 常用于 Android 和 .NET 开发
- `MVVM`: Model-View-ViewModel 常见于 Vue、AngularJS 等框架
- `MVT`: Model-View-Template 常用于 Django

不同架构的区别在于用户界面、业务逻辑、数据层之间如何交互

MVC 和 MVP 强调了控制器/呈现器作为中介的角色，Controller 作为流量控制器，负责协调 Model 和 View。View 可以更主动地从 Model 中获取数据，而 Controller 更多地关注于更新 Model 和选择 View，Presenter 对 View 有更直接的控制，而且业务逻辑几乎全部在 Presenter 中处理。

MVVM 则通过双向数据绑定将视图和模型分离

MVT 则提供了一个清晰的模板层来处理数据展示，需要注意的是，MVT 中的 View 是逻辑控制层，Template 才是类似其它架构中的 View
