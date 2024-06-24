# 引言

API, Application Programming Interface

<https://apievangelist.com/info/101/>

## 测试工具

- cURL
- Postman
- Pytest + Requests

## 测试点

- 参数

输入：比如格式、类型、名称等、长度不一致，边界值、为空、重复等

返回：字段类型，字段值，是否缺少字段

- 权限

比如下单功能要依赖于登陆，否则应该返回异常

- 安全

cookie、header

- 状态

状态码 200 只代表请求返回正常，不代表返回数据正确，还要看自定义的错误码是否符合预期

- 逻辑

## 接口服务

- <https://www.example.com>
- <https://www.example.org>

可用于文档示例的两个 example 域名，由 IANA 提供，无需事先协调或征求许可。

### Swagger Petstore

<https://petstore.swagger.io/>

Swagger 是一组围绕 OpenAPI 规范构建的开源工具，可以帮助您设计、构建、记录和使用 REST API

[Swagger Editor](https://editor.swagger.io/) 是基于浏览器的 API 编辑器，使用 YAML 或 JSON 编写，然后使用 Swagger UI 呈现为交互式文档

官方文档：<https://swagger.io/docs/specification/basic-structure/>

### httpbin

- <http://httpbin.org>
- <https://httpbin.org>

A simple HTTP Request & Response Service.

还提供了一个表单页面：<https://httpbin.org/forms/post>

若国内访问较慢，可本地运行：`docker run -p 80:80 kennethreitz/httpbin`

> 由 Requests 库作者 Kenneth Reitz 使用 Flask 搭建  
> 除此之外，他还搭建了很多人性化的工具，其个人网站：<https://kennethreitz.org>

### Postman Echo

Postman 提供的测试服务，除了 REST 服务外，还支持 WebSocket, gRPC, GraphQ, SocketIO 协议

<https://learning.postman.com/docs/developer/echo-api/>

另外 Postman 社区还有一些公共 API：<https://www.postman.com/explore>

## Mock 数据

### Mockaroo

<https://www.mockaroo.com/>
