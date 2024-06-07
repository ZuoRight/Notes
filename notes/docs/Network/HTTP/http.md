# 组成

## 请求

1. 起始行(HTTP/2中已废除)
2. 头部
3. 空行(CRLF，必须有，空行后面都会被当作实体)
4. 实体(Entity/Body)

### 起始行

- 请求行(GET / HTTP/1.1)
- 状态行(HTTP/1.1 200 OK)

```text
- Method
- URI
- Version
- Status Code
- Reason
```

### 请求方法

不改变状态

- `GET` 获取数据，安全且幂等
- `HEAD` 与GET相同，但是不返回响应体，安全且幂等
- `OPTIONS` 用于验证接口服务是否正常，安全且幂等

可改变状态

- `POST` 创建或更新数据，不安全不幂等
- `PUT` 更新数据，不安全但幂等
- `PATCH` 与PUT类似，通常用于部分更新，不安全但幂等
- `DELETE` 删除数据，不安全但幂等

### Headers

#### 伪头部

方便管理和压缩，HTTP/2中用伪头部形式(:key)替代了起始行，废除了没用的Version和Reason

```text
- :scheme 协议
- :authority 域名
- :path 路径
- :method 请求方法
  - 常用
    - GET：获取资源
    - POST：向资源提交数据(偏create含义)
  - 不常用
    - HEAD：只获取资源响应头
    - PUT：类似POST(偏update含义)
    - CONNECT：要求服务器为客户端和另一台远程服务器建立一条特殊的连接隧道
  - 通常禁用
    - DELETE：删除资源(太危险，一般只做一个标记)
    - OPTIONS：列出可对资源实行的方法(用处不大)
    - TRACE：追踪请求到响应的传输路径(会泄漏网站信息)
  - 扩展(非标准)：MKCOL/COPY/MOVE/LOCK/UNLOCK/PATCH
- :status 状态码
```

#### 未分类

```text
- 没啥用
  - User-Agent：用于描述客户端，但由于历史原因已经非常混乱，基本无用
    - Mozilla/Chrome/Safari/AppleWebKit
    - spider(爬虫)
  - Server 正在提供Web服务的软件名称和版本号，但出于安全考虑通常省略或随便写
  - Connection: keep-alive(长链接，默认)/close(本次通信后关闭连接)
  - Keep-Alive: timeout=value(限定长链接超时时间，约束力较弱)
- 有用
  - Host：因为同一服务器上可能有多台虚拟主机(IP相同，域名不同)，需要告诉服务器由哪个主机处理请求(v1.1要求必须带)
  - Referer: 跳转来源，可用于统计分析和防盗链
  - Referrer Policy: no-referrer-when-downgrade
  - Origin: 发起一个针对跨来源资源共享的请求
  - Location: uri 标记了服务器要求重定向的URI
  - Refresh: n;url=xxx 延迟重定向
  - Via：标识是否经过代理服务器
  - Upgrade-Insecure-Requests 告诉服务器可以处理HTTPS协议，以后发请求的时候不用http而用https
  - Strict-Transport-Security 告诉浏览器必须使用HTTPS访问资源，但为了兼容HTTP协议，所以规定日期内浏览器需要自动把请求中的http转换为https协议发起请求，免去中间人攻击可能，少一次服务端重定向，加快连接速度
  - Retry-After 返回503错误时提示多久后重试
```

#### 内容相关

```text
- Date: 报文创建时间
- Vary：记录服务器在内容协商过程中参考的字段
- 内容类型(MIME type)
  - Accept
  - Content-Type
    - text/html(超文本文档)/plain(纯文本)/css(样式表)
    - image/gif/jpeg/png
    - audio/mpeg
    - video/mp4
    - application/javascript/pdf/xml(数据格式不固定)
    - application/json;charset=UTF-8(Chrome请求体标识为：Request Payload)
    - application/x-www-form-urlencoded;charset=UTF-8(表单，Chrome请求体标识为：Form Data)
    - application/octet-stream(不透明的二进制数据)
- 压缩方式
  - Accept-Encoding：为空表示客户端不支持压缩数据
  - Content-Encoding：为空表示服务端没有压缩数据
    - gzip：互联网上最流行的压缩格式，仅对文本文件有较好的压缩率，压缩率60%
    - deflate：流行程度次之
    - br：专为HTTP优化的新压缩算法(Brotli)
- 自然语言类型(type-subtype)
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
  - Content-Length：没有该字段表示不定长
- 分块传输(不定长时)
  - Transfer-Encoding: chunked
- 范围请求
  - Accept-Ranges
    - bytes(告知客户端支持范围请求)
    - none或者没有该字段(告知客户端不支持范围请求)
  - Ranges: bytes=x-y
  - Content-Range: bytes x-y/length(总长度)
  - multipart/byteranges：表示实体由多段序列组成
```

#### Cookie相关(常用来标记用户或授权会话)

```text
- Set-Cookie 服务端可以设置多个身份字段
  - Expires 过期时间
  - Max-Age 有效期(优先级高)
  - Domain 作用域
  - Path 作用路径
  - HttpOnly 只能通过浏览器HTTP协议传输Cookie，禁止其他方式访问
  - SameSite 可以防范XSRF(跨站请求伪造)攻击
    - None 允许同站跨站都会发送
    - Lax 允许同站和GET/HEAD等安全方法跨站，但禁止POST跨站发送
    - Strict 只允许同站，禁止跨站发送
  - Secure 表示这个Cookie仅能用HTTPS协议加密传输
- Cookie 客户端只有一个该字段，多个身份可以用分号分割
```

#### Cache相关

```text
- Cache-Control
  - 缓存有效期
    - max-age=n1：有效期n秒，<=0立即失效
    - max-stale=n2：过期时间延长为n1+n2秒
    - min-fresh=n3：过期时间缩短为n1-n3秒
    - s-maxage=n：仅限制在代理服务器上的有效期
    - Expires
  - 如何使用缓存
    - no-store：不允许缓存
    - no-cache(页面强制刷新时触发)：需要先请求服务器验证是否失效
    - must-revalidate：缓存过期后再请求服务器验证是否失效
    - proxy-revalidate：代理服务器的缓存过期就必须回源服务器验证
  - 缓存类别(RFC没有明确规定默认类别)
    - public：缓存可以在代理服务器保存(专栏里说默认是这个)
    - private：缓存只能在客户端保存(百度百科说默认是这个)
  - no-transform：不允许代理对缓存数据做优化
  - only-if-cached：仅接受代理服务器上缓存的数据

- 验证缓存是否失效
  - 普通请求(页面后退/前进/重定向时触发)
    - 不会请求服务器，直接检查缓存(未失效显示：from disk cacahe)
  - 条件请求(页面刷新时触发)
    - if-Modified-Since
      - Last-modified：根据文件最后修改时间验证
    - If-None-Match
      - ETag
        - "根据语义级是否有修改验证"
        - W/"根据字节级是否有修改验证"

- X-Cache：标识代理服务器缓存是否命中
- X-Hit：标识代理服务器缓存的命中率
- Pragma:no-cache 与Cache- Control:no-cache相同
```

#### Chrome特有头

```text
- Sec-Fetch-Dest: 请求的目的地是哪里
- Sec-Fetch-Mode: 请求模式
- Sec-Fetch-Site: 请求来源与目标之间的关系
- Sec-Fetch-User: 用于判断触发方式是否合法(目的地为document时才有)
```