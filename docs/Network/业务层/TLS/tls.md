# TLS

计算机科学领域里没有什么是引入中间层不能解决的，如果有，那就再加一层。

HTTPS(HyperText Transfer Protocol Secure) 就是在 HTTP 和 TCP 之间增加了 SSL/TLS 协议，以此来提高数据传输的安全性。

TLS(Transport Layer Security) 是从网景公司发明的 SSL(Secure Sockets Layer) 经历了 v1~v3 版本迭代演变而来

OpenSSL 是 SSL/TLS 的具体实现，是一个开源的密码学工具包。

![加密](https://image.zuoright.com/加密.png)

```text
加密数据，对称加密传输效率高，所以用于加密数据，比如 AES
身份验证，数字证书机制验证服务端的非对称加密公钥是可信的
传输数据，非对称加密更安全，所以用于传输预主密钥（Premaster secret），TLS 早期版本使用 RSA，后期结合 DH(Diffie-Hellman) 一起使用
防篡改：拥有公钥的人都可以加密传输，所以要使用哈希摘要和非对称签名防篡改
```

在 TCP 连接建立之后，开始进行 TLS 握手

> 参考：<https://www.thesslstore.com/blog/explaining-ssl-handshake/>

```text
1. Client Hello
2. Server Hello
3. 客户端密钥交换，证书验证通过后，客户端会随机生成一个预主密钥，使用证书中的公钥进行加密后发送到服务器，服务端使用私钥解密
4. 然后两端便都可使用预主密钥派生出相同的会话密钥（Session key）
```

![20250608193132](https://image.zuoright.com/20250608193132.png)

TLS 1.2 中完成握手需要两次往返，TLS 1.3 中将一些可同时发生的步骤合并，简化为一次半往返

## 交换密码套件和参数

客户端有不同的 Web 浏览器以及移动端等，服务器端有 Windows Server、Apache 和 NGINX 等，所以 TLS 握手的第一步需要双端共享其参数以便找到它们共同支持的密码套件（cipher suite），达成一致后，服务器就会向客户端发送其 SSL 证书

TLS 1.2 有 37 种密码套件，比如

![20250608194318](https://image.zuoright.com/20250608194318.png)

```text
TLS 是协议
ECDHE 是密钥交换算法
ECDSA 是身份验证算法
AES 128 GCM 是对称加密算法
SHA256 是哈希算法
```

TLS 1.3 淘汰了不安全的算法，将支持的密码套件从 37 个减少到 5 个，并缩小了密码套件的大小

![20250608194556](https://image.zuoright.com/20250608194556.png)

```text
TLS 是协议
AES 256 GCM 是带关联数据的经过身份验证的加密（AEAD）算法
SHA384 是哈希密钥派生函数（HKFD）算法
```

## 对一方或双方进行身份验证

通常客户端验证服务端的证书通过后，服务端用账号和密码等手段就能够确认用户的真实身份。但为了防止账号、密码被盗，有时服务端也要校验客户端的证书，这样会更加安全，比如网上银行使用 U 盾给用户颁发客户端证书，但由于服务器端需要维护所有客户端的证书，无疑增加了很多消耗，所以大多数情况下一般不会双向校验。

## 创建/交换对称密钥

从历史上看，密钥交换的两个主要选项是 RSA 和 Diffie-Hellman （DH），现在你经常看到 DH 与椭圆曲线相关联

- 基于 RSA 的握手

![20250607195819](https://image.zuoright.com/20250607195819.png)

- 基于 DH 的握手

![20250607200618](https://image.zuoright.com/20250607200618.png)
