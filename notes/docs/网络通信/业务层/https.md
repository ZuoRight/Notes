# HTTPS

TLS：开源密码学工具包OpenSSL是SSL/TLS的具体实现

- TLS 1.0/SSLv3.1
- TLS 1.1(2006)  
- TLS 1.2(2008)  
- TLS 1.3(2018)

> 计算机科学领域里的任何问题，都可以通过引入一个中间层来解决，如果一个中间层不行，那就再加一个中间层。

HTTPS就是在HTTP和TCP之间增加了SSL/TLS协议，以此来提高数据传输的安全性。

SSL/TLS最初叫做SSL(Secure Sockets Layer)，是由网景公司发明，经历了v1~v3版本的迭代，然后改名为TLS(Transport Layer Security)继续迭代，目前最常用版本为TLSv1.2。

TLS 主要由记录协议、警报协议、握手协议、以及密码变更协议四个子协议组成，然后还依赖一些开源的密码学底层库(比如最常用的OpenSSL)实现各种加密算法等

## TLS四次握手

客户端会和服务端共同商议出使用什么对称加密算法

> TLS 收发数据的基本单位：记录（record）

具体的流程可以参照这个流程图来看

![x](https://static001.geekbang.org/resource/image/10/7e/10315ffa19492462cadfbdfb3113987e.jpg)

以上流程只有客户端对服务端做了认证，而服务端没有对客户端的身份做认证，称之为单向认证。但通常单向认证通过后就已经建立了安全通信，用账号、密码等手段就能够确认用户的真实身份。但为了防止账号、密码被盗，有时（比如网上银行）还会使用U盾给用户颁发客户端证书，实现“双向认证”，这样会更加安全。

> 参考：[七次握手，九倍往返时延（RTT，Round-Trip Time）](https://draveness.me/whys-the-design-https-latency/)
