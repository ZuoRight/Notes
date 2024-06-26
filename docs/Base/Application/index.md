---
comments: true
hide:
  - feedback
---

# 引言

## 架构

- 去中心化
    - P2P（Peer-to-Peer）不区分客户端和服务端，两端是对等关系
- 中心化
    - C/S（Client/Server）区分为客户端和服务端，客户端是主机上的程序
    - B/S（Browser/Server）区分为客户端端和服务端，客户端是浏览器

无论服务端，还是客户端，运行的单位都是进程。

![20240616204339](https://image.zuoright.com/20240616204339.png)

一台机器上可以部署运行多个服务，比如同时开启了 SSH 服务和 HTTP 服务等。

一个客户端同一个时刻也可以建立多个到不同服务器的连接，比如同时购物、刷视频、看新闻等。

![20220908142430](http://image.zuoright.com/20220908142430.png)

客户端发出一个 HTTP 请求，到达应用程序，为了提高性能，可以加一层 Nginx 反向代理处理静态资源，还可以做负载均衡等

## 前后端分离

- 未分离

后端控制前端的展示，渲染页面或重定向，

经典的 MVC 框架时代：Servlet + Jsp + JavaBean

- 半分离

前端负责开发页面，通过 Ajax 获取数据，采用 Dom 操作对页面进行数据绑定，最终由前端把页面渲染出来

后端框架为主的 Java 框架时代：SSM（Spring + SpringMVC + Mybatis）, SSH（Spring + Struts2 + Hibernate）

- 分离

无论哪种前端，所需的数据基本相同，后端仅需开发一套逻辑对外只提供数据即可

前端框架为主的 MVVM 时代：VueJS、AngularJS、ReactJS

再到 Node.js 引领的全栈时代

## 框架模型

不同模型的区别在于用户界面、业务逻辑、数据层之间如何交互

- `MVC`: Modle-View-Controller 经典 Web 应用模型
- `MVP`: Model-View-Presenter 常用于 Android 和 .NET 开发
- `MVVM`: Model-View-ViewModel 常见于 Vue、AngularJS 等框架
- `MVT`: Model-View-Template 常用于 Django

MVC 的 Controller 作为流量控制器，负责协调 Model 和 View，View 可以更主动地从 Model 中获取数据。

MVP 的 Presenter 对 View 有更直接的控制，而且业务逻辑几乎全部在 Presenter 中处理。

MVVM 则通过双向数据绑定将视图和模型分离

MVT 则提供了一个清晰的模板层来处理数据展示，需要注意的是，MVT 中的 View 是逻辑控制层，Template 才是类似其它模型中的 View

![20240625232057](https://image.zuoright.com/20240625232057.png)
