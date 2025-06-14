---
comments: true
hide:
  - feedback
---

# 引言

## 架构

- P2P（Peer-to-Peer）不区分客户端和服务端，两端是对等关系，去中心化的
- C/S（Client/Server）区分为客户端和服务端，客户端是主机上的程序
- B/S（Browser/Server）区分为客户端和服务端，客户端是浏览器

![20240627005912](https://image.zuoright.com/20240627005912.png)

`浏览器 -> Web 应用防火墙（WAF） -> Web 服务器 -> 应用服务器（网关协议） -> Web 框架`

- Web 服务器，也叫 HTTP 服务器，主要处理静态资源

```text
比如 Apache、Nginx、IIS 等
有些应用框架提供了内置的 Web 应用服务器可独立运行，比如 Django，但通常只用于开发环境
```

- 应用服务器，主要处理动态资源，接收并解析 HTTP 请求

```text
最初网站都是静态的，只需要 Web 服务器即可，变为动态网站后，应用需要通过一套标准的网关协议与 HTTP 服务器进行交互
早期 PHP 开发网站主要使用通用网关接口 CGI（Common Gateway Interface），FastCGI 为其增强版，提高了性能
随着 Web 应用的复杂性和性能要求的提高，出现了多种现代接口和应用服务器，提供更高级的功能和更高的性能，不同语言有各自独立的实现规范
Java EE 的 Servlet 和 JSP，常见的封装实现有 Jetty、Tomcat 等
微软的 ASP、ASP.NET，封装实现有 IIS
Python 的 WSGI，常见的封装实现有 uWSGI、Gunicorn 等
```

![20220908142430](https://image.zuoright.com/20220908142430.png)

无论服务端，还是客户端，运行的单位都是进程。

![20240616204339](https://image.zuoright.com/20240616204339.png)

一台机器上可以部署运行多个服务，比如同时开启了 SSH 服务和 HTTP 服务等。

一个客户端同一个时刻也可以建立多个到不同服务器的连接，比如同时购物、刷视频、看新闻等。

## 框架模型

不同模型的区别在于用户界面、业务逻辑、数据层之间如何交互

### MVC

Modle-View-Controller

![20240628131242](https://image.zuoright.com/20240628131242.png)

- View 传送指令到 Controller
- Controller 完成业务逻辑后，要求 Model 改变状态
- Model 将新的数据发送到 View，用户得到反馈

MVC 可以通过 View 接受指令，也可以通过 Controller 接受指令，其它模型基本都是基于 MVC 演变的

### MVP

Model-View-Presenter

![20240628133700](https://image.zuoright.com/20240628133700.png)

View 与 Model 不直接发生联系，都通过 Presenter 传递，常用于 Android 和 .NET 开发

### MVVM

Model-View-ViewModel

与 MVT 唯一的区别就是采用双向绑定，常见于 Vue、AngularJS 等框架

### MVT

Model-View-Template

![20240625232057](https://image.zuoright.com/20240625232057.png)

- Template 类似 MVC 中的 View
- View 逻辑控制层，控制逻辑
- Model

## 前后端分离

### 未分离

后端控制前端的展示，渲染页面或重定向，

经典的 MVC 框架时代：Servlet + Jsp + JavaBean

### 半分离

前端负责开发页面，通过 Ajax 获取数据，采用 Dom 操作对页面进行数据绑定，最终由前端把页面渲染出来

后端框架为主的 Java 框架时代：SSM（Spring + SpringMVC + Mybatis）, SSH（Spring + Struts2 + Hibernate）

### 分离

无论哪种前端，所需的数据基本相同，后端仅需开发一套逻辑对外只提供数据即可

从前端框架为主的 MVVM 时代，再到 Node.js 引领的全栈时代

SPA(Single Page Application)

- Angular：由 Google 开发
- React：由 Facebook 开发
- Vue：由 Evan You（尤雨溪）开发

SSR(Server Side Rendering)

- React 生态的 Next.js
- Vue 生态的 Nuxt
