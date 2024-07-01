# 鉴权

登录系统：注册 -> 认证 -> 授权 -> 鉴权

- 认证 identification 你是谁？

通过用户名、密码、指纹或其他方式，系统验证用户提供的凭证是否与系统中存储的信息匹配。

- 授权 authorization，能干啥？

谁拥有什么权去操作哪些资源

授权是在认证之后进行的，基于用户的权限和角色，控制用户可以访问哪些资源以及可以执行哪些操作。

## Session

```
1. 用户向服务器发送用户名和密码。
2. 服务器验证通过后，会建立一个 Session，保存当前对话的相关数据，比如用户角色、登录时间等等。
3. 服务器向用户返回 session_id 写入用户的 Cookie。
4. 用户随后的每一次请求，都会通过 Cookie，将 session_id 传回服务器。
5. 服务器收到 session_id，找到保存的 Session 数据，由此得知用户的身份。
```

```text
- Set-Cookie 服务端返回
  - Expires 过期时间
  - Max-Age 有效期(优先级高)
  - Domain 作用域
  - Path 作用路径
  - HttpOnly 禁止 JS 等其他方式访问
  - SameSite 可以防范XSRF(跨站请求伪造)攻击
    - None 允许同站跨站都会发送
    - Lax 允许同站和GET/HEAD等安全方法跨站，但禁止POST跨站发送
    - Strict 只允许同站，禁止跨站发送
  - Secure 表示这个Cookie仅能用HTTPS协议加密传输

- Cookie 客户端根据服务端返回的 Set-Cookie 保存 Cookie
  - Expires 过期时间，默认与 Session 一起失效
  - Max-Age 有效期(优先级高)
```

这种模式在跨域或分布式架构中有一些问题，比如要实现用户只要在其中一个网站登录，再访问另一个网站就会自动登录这种场景，就需要多个网站的服务器间可以共享 session，可以通过写入 redis 等来实现

## Token

也可以使用 token 来实现，token，也称作令牌

通常包含以下信息

```text
uid：唯一标识
time：当前时间的时间戳
sign：签名，使用 hash/encrypt 压缩成定长的十六进制字符串，防止恶意拼接
固定参数：可选，避免重复
```

最常见的实现就是 JWT(Json Web Tokens)

![20240619133556](https://image.zuoright.com/20240619133556.png)

服务器认证以后，生成一个 JSON 对象，返回给用户，用户与服务端通信的时候，都要发送这个 JSON 对象，这样服务器就不保存任何 session 数据了，即所谓的无状态

类似于临时的证书签名，保存在 localStroage 等容器中，CPU 加密，服务端解密，不存在负载均衡问题，解决了跨域认证。

## OAuth

参考：<https://www.ruanyifeng.com/blog/2019/04/oauth-grant-types.html>

OAuth 引入了一个授权层，用来分离两种不同的角色：客户端和资源所有者。资源所有者同意以后，资源服务器可以向客户端颁发令牌。客户端通过令牌，去请求数据。

OAuth 2.0 规定了四种获得令牌的方式

- 授权码（authorization-code）应用先申请授权码，然后再用该码获取令牌
- 密码式（password）适用于高度信任的应用，允许用户把用户名和密码告诉应用，用于申请令牌
- 隐藏式（implicit）适用于纯前端应用，直接向前端颁发令牌
- 客户端凭证（client credentials）适用于命令行应用
