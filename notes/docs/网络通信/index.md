# 引言

Resources 资源，就是网络上的一个实体，它可以是一段文本、图片、音频、视频、服务等等

URI(Uniform Resource Identifier) 统一资源标识符，是一种抽象的定义，只要能唯一标识资源的都可以叫URI，可以用地址定位，即URL，也可以用名称定位，即URN

- URL(Uniform Resource Locator) 统一资源定位符，通过地址定位标识资源，最常用的就是网址
- URN(Uniform Resource Name) 统一资源名称，比如书籍的isbn号

url组成：`scheme:[subscheme]://[username:password@]host:port/path?query#fragment`

```text
scheme 协议，比如：http, https
subscheme 子协议，用于区分数据库，比如：jdbc:mysql://localhost:3306/nemo?user=root&password=123456
host 主机名或IP
```

资源可以有不同的表现形式，称之为表现层，比如文本可以是TXT、XML、JSON、HTML等，图片可以是PNG也可以是JPG等

可以在请求头中使用`Accept`和`Content-Type`字段指定表现形式以及版本号等

访问一个网站的资源，即客户端与服务端交互，涉及数据和状态的转化，HTTP协议是一个无状态协议，所以状态只能保存在服务端，客户端如果想通过HTTP请求来改变状态，可以遵循RESTful设计原则来实现

> REST(Representational State Transfer) 表现层状态转移

请求方法

- `GET` 获取数据，安全且幂等
- `HEAD` 与GET相同，但是不返回响应体，安全且幂等
- `OPTIONS` 用于验证接口服务是否正常，安全且幂等
- `POST` 创建或更新数据，不安全不幂等
- `PUT` 更新数据，不安全但幂等
- `PATCH` 与PUT类似，通常用于部分更新，不安全但幂等
- `DELETE` 删除数据，不安全但幂等

RESTful的一些设计原则

- API中不应该包含如版本号等资源表示形式，可以放到请求头中
- API中不应该包含动词，可以放到请求体中
