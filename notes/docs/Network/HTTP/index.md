# 引言

HyperText Transfer Protocol 超文本传输协议

HTTP 的特点是

- 建立在 TCP 之上（通过 Socket 来使用 TCP）
- 无连接（TCP 就是它的连接）
- 无状态（后来加了 Cookies、Session 技术，用 KeepAlive 来维持状态）
- 请求-响应模式

> Socket 做为套接层 API，本身不是协议，只规定了 API

![20230823094945](https://image.zuoright.com/20230823094945.png)

由于HTTP 协议是一个无状态协议，所以状态只能保存在服务端，访问一个网站的资源，即客户端与服务端交互，涉及数据和状态的转化，客户端如果想通过HTTP请求来改变状态，可以遵循 RESTful 设计原则来实现

> REST(Representational State Transfer) 表现层状态转移

- API 中不应该包含如版本号等资源表示形式，可以放到请求头中
- API 中不应该包含动词，可以放到请求体中

## 发展

- HTTP/1.1
  > Port：80  
  > 对应协议：RFC2616，补充协议：RFC7231、RFC6265
- HTTPS
  > Port：443  
  > 安全：使用 TLS
- HTTP2/SPDY
  > Port：沿用 HTTP/HTTPS  
  > 安全：强制使用TLSv1.2  
  > 效率：多路复用，头部使用了HPACK压缩算法
- HTTP3/QUIC
  > Port：没有指定默认端口号  
  > 安全：强制使用TLSv1.3  
  > 效率：传输基于QUIC(Quick UDP Internet Connections)

## 认证 identification

根据一些信息确认用户的身份，比如用户名密码，二维码，指纹等等

## 授权 authorization

资源所有者授予执行者操作资源的权限

授信媒介

- session
- cookies
- token

### session

如果想让两次请求联系起来，或者说共享数据，说着说保持会话状态，需要使用某种手段，比如session，而cookie是实现session最常用的方案之一

服务端接收到请求后，会建立一个session，并返回响应，响应头中包含了set-cookie头部，头部中包含了sessionId
session包含了用户的认证信息和登录状态等信息
服务端接收cookie后解析出sessionId，再去session列表中查找，才能找到相应session

### token

token，也称作令牌，由uid+time+sign+[固定参数]，类似于临时的证书签名（是一种服务端无状态的认证方式，即服务端不会保存），保存在localStroage等容器中，cpu加密，服务端解密，不存在负载均衡问题，这个方法叫做JWT(Json Web Token，一种跨域认证方案)

uid：唯一标识
time：当前时间的时间戳
sign：签名，使用hash/encrypt压缩成定长的十六进制字符串，防止恶意拼接
固定参数：可选，避免重复

## 鉴权 authentication

验证权限

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
