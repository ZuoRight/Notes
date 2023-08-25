# 引言

访问一个网站的资源，即客户端与服务端交互，涉及数据和状态的转化

HTTP协议是一个无状态协议，所以状态只能保存在服务端，客户端如果想通过HTTP请求来改变状态，可以遵循 RESTful 设计原则来实现

> REST(Representational State Transfer) 表现层状态转移

- API 中不应该包含如版本号等资源表示形式，可以放到请求头中
- API 中不应该包含动词，可以放到请求体中

## 请求方法

不改变状态

- `GET` 获取数据，安全且幂等
- `HEAD` 与GET相同，但是不返回响应体，安全且幂等
- `OPTIONS` 用于验证接口服务是否正常，安全且幂等

可改变状态

- `POST` 创建或更新数据，不安全不幂等
- `PUT` 更新数据，不安全但幂等
- `PATCH` 与PUT类似，通常用于部分更新，不安全但幂等
- `DELETE` 删除数据，不安全但幂等

## 状态码

> 注意，状态码仅表示状态，具体还要看返回内容中自定义的错误码

1xx：提示信息，表示协议处理的中间状态，还需要后续的操作，不常见

- 101 更换协议后继续通信

2xx：成功，报文已经收到并被正确处理

- 200 一切正常，通常会返回body
- 204 正常，不会返回body
- 206 正常，只返回部分数据(通常出现在范围请求时)

3xx：重定向，资源位置发生变动，需要客户端重新发送请求

- 301 请求资源已不在，永久重定向
- 302 请求资源还在，临时重定向，比如系统维护时
- 304 重定向到了缓存(一般与If-Modified-Since同在)

4xx 客户端错误

- 400 通用错误码，请求报文有误，服务器无法处理
- 403 服务器禁止访问资源，其实不属于客户端错误
- 404 资源未找到，已经被用滥
- 405～431 一般都会返回较明确的备注提示

5xx 服务端错误

- 500 通用错误码，服务端错误
- 501 表示还不支持，类似即将开业，敬请期待
- 502 一般为网关或者代理服务器出错
- 503 网络服务较忙，是一个临时状态(此时会出现Retry-After头告诉多久后重试)

## 测试服务

- <https://www.example.org>

This domain is for use in illustrative examples in documents.

You may use this domain in literature without prior coordination or asking for permission.

- <https://httpbin.org>

由 Requests 库作者 Kenneth Reitz 开发

> 除此之外，他还搭建了很多人性化的工具，其个人网站：<https://kennethreitz.org>

可以本地运行服务：`docker run -p 80:80 kennethreitz/httpbin`
