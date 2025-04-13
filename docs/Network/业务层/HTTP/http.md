# HTTP

HTTP 是无状态的，后来加了 Cookies、Session 技术，用 KeepAlive 来维持状态

由于 HTTP 协议是一个无状态协议，所以状态只能保存在服务端，访问一个网站的资源，即客户端与服务端交互，涉及数据和状态的转化，客户端如果想通过 HTTP 请求来改变状态，可以遵循 RESTful 设计原则来实现

> REST(Representational State Transfer) 表现层状态转移

- API 中不应该包含如版本号等资源表示形式，可以放到请求头中
- API 中不应该包含动词，可以放到请求体中

## 请求方法

### GET

主要用于获取资源

### POST

用于向服务器提交数据，无论是创建还是更新，都可以用 POST

### HEAD

与 GET 相同，但只返回头信息

常用于判断某个资源是否存在

或者用于判断一个资源的大小，只需要获取返回头中的 `content-lenght` 字段，而不需要实际下载

### OPTIONS

用于验证接口服务是否正常

通常复杂的请求都需要先调用一下 OPTIONS 方法发起预检，比如跨域时，会先发起一个 CORS 预检请求，服务端返回允许跨域才能发起后续请求

请求头中的 `Access-Control-Max-Age` 字段可以优化 OPTIONS 请求，该字段值表示多少秒内不需要再使用 OPTIONS 进行请求，值为 `-1` 时表示禁用缓存，每次请求都需要先预检。

### 其它协议

- PUT 更新数据
- PATCH 与 PUT 类似，通常用于部分更新
- DELETE 删除数据

这几个协议都很少用，基本都用 POST 替代。

### Get 和 Post 的区别

GET 是幂等的，POST 是非幂等的。

GET 请求默认会缓存，POST 不会主动缓存

Get 主要用于获取数据，Post 主要用于提交和修改数据

Get 的参数拼接在 URL 中传输，POST 在 Body 中传输。但本质上其实没啥区别，只要服务端支持，协议层都支持带查询参数和发送数据。只是规范或者说报文格式不同。

一般浏览器或者服务端会对 URL 做长度限制，但本身协议层没有限制长度。

比如传输图片，可以用 file 或 base64 的形式，图片越大 base64 字符越长，所以用 GET 方法只能传输一些非常小的图片。

### 关于幂等性

在数学中，幂等操作指的是，函数 $f$ 满足 $f(f(x)) = f(x)$。例如，绝对值函数 $abs(x)$ 是幂等的，因为 $abs(abs(x)) = abs(x)$

在计算机科学中幂等性是指一个操作，无论执行多少次，其结果都应该是相同的，具有相同的效果。换句话说，重复执行这个操作，不会对系统状态产生额外的影响。

所以 POST 请求是非幂等，比如创建表单，多次提交可能会创建多个表单

而 GET、PUT、DELETE 等请求都是幂等的，因为 GET 类似只读，相同的资源请求多少次都一样，DELETE 只会删除一次，PUT 只会更新同一资源。

## 状态码

Status Code 用于表示请求的结果

注意，状态码仅表示状态，具体还要看返回内容中自定义的错误码

参考

- <https://www.cheat-sheet.cn/post/http-status-code/>
- <https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status>

### 1xx

提示信息，表示协议处理的中间状态，还需要后续的操作

```text
101 更换协议后继续通信
```

### 2xx

成功，报文已经收到并被正确处理

```text
200 一切正常，通常会返回 body
204 正常，不会返回 body
206 正常，只返回部分数据，通常出现在范围请求时
```

### 3xx

重定向，资源位置发生变动，需要客户端重新发送请求

```text
301 请求资源已不在，永久重定向
302 请求资源还在，临时重定向，比如系统维护时
304 重定向到了缓存
    - If-Modified-Since
    - If-None-Match 与 ETag 一起使用，提供更精确的缓存验证
```

304 请求

```shell
# 第一次请求的响应头中包含 Last-Modified
HTTP/1.1 200 OK
Last-Modified: Wed, 21 Jul 2024 19:15:56 GMT

# 后续请求
GET /style.css HTTP/1.1
If-Modified-Since: Wed, 21 Jul 2024 19:15:56 GMT
# 请求返回
HTTP/1.1 304 Not Modified  # 表示请求的资源自上次访问以来没有被修改
```

### 4xx

客户端错误

```text
400 通用错误码，请求报文有误，服务器无法处理
401 未授权
403 服务器禁止访问资源，其实不属于客户端错误
404 资源未找到，已经被用滥
405～431 一般都会返回较明确的备注提示
```

### 5xx

服务端错误

```text
500 通用错误码，服务端错误
501 表示还不支持，类似即将开业，敬请期待
502 一般为网关或者代理服务器出错
503 网络服务较忙，是一个临时状态，此时会出现 Retry-After 头告诉多久后重试
```

## Headers

### 伪头部

HTTP/2 中用伪头部形式（`:key`）替代了起始行，废除了没用的 `Version` 和 `Reason`，方便管理和压缩

```text
- :scheme 协议
- :authority 域名
- :path 路径
- :method 请求方法
- :status 状态码
```

### Content-Type

- 多部分类型（Multipart Types）

```text
multipart/form-data：用于表单提交，特别是包含文件上传的表单
multipart/byteranges：响应中包含多个部分的字节范围
```

- 应用类型（Application Types）

```text
application/x-www-form-urlencoded：表单提交数据，通常用于HTML表单
application/octet-stream：二进制数据流，通常用于文件下载
application/json：JSON格式
application/xml：XML格式
application/pdf：PDF文档格式
application/zip：ZIP压缩文件格式
```

- 文本类型（Text Types）

```text
text/plain：纯文本格式
text/html：HTML格式
text/css：CSS格式
text/javascript：JS代码格式
```

- 图像类型（Image Types）

```text
image/jpeg：JPEG图像格式
image/png：PNG图像格式
image/gif：GIF图像格式
image/webp：WebP图像格式
```

- 音频类型（Audio Types）

```text
audio/mpeg：MP3音频格式
audio/wav：WAV音频格式
audio/ogg：OGG音频格式
```

- 视频类型（Video Types）

```text
video/mp4：MP4视频格式
video/webm：WebM视频格式
video/ogg：OGG视频格式
```

### 内容相关

```text
- Date: 报文创建时间

- Vary：记录服务器在内容协商过程中参考的字段

- 内容类型(MIME type)
  - Accept

- 压缩方式
  - Accept-Encoding：为空表示客户端不支持压缩数据
  - Content-Encoding：为空表示服务端没有压缩数据
    - gzip：互联网上最流行的压缩格式，仅对文本文件有较好的压缩率，压缩率60%
    - deflate：流行程度次之
    - br：专为HTTP优化的新压缩算法(Brotli)

- 自然语言类型 type-subtype
  - Accept-Language
  - Content-Language
    - en(任意英文)
    - en-US(美式英文)
    - en-GB(英式英文)
    - zh-CN(简体中文)

- 字符集(现在的浏览器均支持多种字符集，通常无需标识)
  - Accept-Charset
  - "charset=xxx"(服务端字符集定义在Content-Type字段中)
    - utf-8
    - gbk

- 长度
  - Content-Length：表示报文长度（压缩后的），没有该字段表示不定长

- 分块传输(不定长时)
  - Transfer-Encoding: chunked

- 范围请求
  - Accept-Ranges
    - bytes 告知客户端支持范围请求
    - none 或者没有该字段，告知客户端不支持范围请求
  - Ranges: bytes=x-y 视频播放位置
  - Content-Range: bytes x-y/length(总长度)
  - multipart/byteranges：表示实体由多段序列组成

- Etag: 资源的版本，判断资源是否更新
```

### Referer

```text
- Referer: 用户跳转来源，可用于统计分析和防盗链
- Referrer Policy: 控制 Referrer 信息传不传、传哪些信息、在什么时候传的策略
    - no-referrer 从不发送
    - no-referrer-when-downgrade 仅当协议降级时不发送，一般默认是这个
    - origin 发送但只包含 host 部分
    - strict-origin-when-cross-origin 只在跨域时发送，只包含 host
    - unsafe-url 统统发送，所以可能不安全
```

### 未分类

```text
- User-Agent：用户代理，由于历史原因已经非常混乱，基本无用
    - 浏览器，比如 Mozilla/Chrome/Safari/AppleWebKit
    - 爬虫 Spider，robots.txt 规定了哪些该爬哪些不该爬
    - 邮箱客户端
- Server 正在提供Web服务的软件名称和版本号，但出于安全考虑通常省略或随便写
- Connection: keep-alive(长链接，默认)/close(本次通信后关闭连接)
- Keep-Alive: timeout=value(限定长链接超时时间，约束力较弱)
- Host：因为同一服务器上可能有多台虚拟主机(IP相同，域名不同)，需要告诉服务器由哪个主机处理请求(v1.1要求必须带)
- Origin: 发起一个针对跨来源资源共享的请求
- Location: uri 标记了服务器要求重定向的URI
- Refresh: n;url=xxx 延迟重定向
- Via：标识是否经过代理服务器
- Upgrade-Insecure-Requests 告诉服务器可以处理HTTPS协议，以后发请求的时候不用http而用https
- Strict-Transport-Security 告诉浏览器必须使用HTTPS访问资源，但为了兼容HTTP协议，所以规定日期内浏览器需要自动把请求中的http转换为https协议发起请求，免去中间人攻击可能，少一次服务端重定向，加快连接速度
- Retry-After 返回503错误时提示多久后重试
```

### Chrome特有头

```text
- Sec-Fetch-Dest: 请求的目的地是哪里
- Sec-Fetch-Mode: 请求模式
- Sec-Fetch-Site: 请求来源与目标之间的关系
- Sec-Fetch-User: 用于判断触发方式是否合法(目的地为document时才有)
```
