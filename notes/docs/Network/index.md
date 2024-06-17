# 引言

互联网 Internet（音译：因特网），通常认为，互联网起源于阿帕网（ARPANET）

常见的互联网服务有：万维网 WWW、文件传输 FTP、电子邮件 E-mail 等

## 网络分层

阿帕网（互联网的前身，大概1970年）使用网络控制协议（Network Control Protocol, NCP）来连接不同的计算机进行通信，1974 年 NCP 的两位开发者发表了以分组、序列化、流量控制、超时和容错等为核心的一种新型网络互联协议套件（IPS）于 1983 年正式取代 NCP，即后来的 TCP/IP 协议簇。

在 TCP/IP 协议的基础之上，1984 年 ISO 又推出了开放式系统互联模型：OSI(Open System Interconnection)。

OSI 总共分为七层，每一层明确了编号，描述的网络更加完整。

但无论 TCP/IP 还是 OSI 都只是参考模型，并不是标准，无论是四层、五层、还是七层，本质都是一样的。

![20240618001433](https://image.zuoright.com/20240618001433.png)

### 各层常见协议

- L7 应用层：提供网络服务的接口，直接与用户应用程序交互
    - HTTP：用于Web服务的半双工传输协议
    - WebSocket：用于Web服务的全双工传输协议
    - FTP：用于文件传输
    - SMTP：用于电子邮件传输
    - DNS：将域名解析为IP地址
    - POP3/IMAP：用于电子邮件的接收
    - SSH：用于安全的远程登录
    - Telnet：用于远程控制协议，但安全性较差
- L6 表示层：负责数据的格式化、加密和解密，提供数据表示的独立性
    - SSL/TLS：提供数据加密和安全通信
    - JPEG、GIF：用于图像数据的格式化和压缩
    - MIME：用于邮件附件的格式化
    - ASCII、EBCDIC：用于文本数据的编码格式
- L5 会话层，不同机器上的用户之间建立及管理会话：RPC、SQL、NFS
    - NetBIOS：用于会话层连接和数据传输的API
    - RPC：允许程序在网络上的另一台计算机上执行代码
    - PPTP：用于虚拟专用网（VPN）的协议
- L4 传输层，屏蔽有无连接的差异，面向进程：TCP、UDP、SPX
    - TCP：提供可靠的、面向连接的数据传输
    - UDP：提供无连接的、不可靠的数据传输，适用于实时应用
    - SCTP：用于多流的数据传输，提供更高级的传输控制
- L3 网际层，：IP、ARP
    - IP：IPv4 和 IPv6，负责数据包的寻址和路由
    - ICMP：用于传输控制消息，如ping命令
    - ARP：将IP地址转换为MAC地址
    - NAT：转换私有IP地址和公共IP地址
    - OSPF：一种链路状态路由协议
    - BGP：一种自治系统间的路由协议
- L2 数据链路层
    - Ethernet：IEEE 802.3，管理MAC地址及以太网帧的传输
    - Wi-Fi：IEEE 802.11，管理无线数据帧传输和MAC地址
    - PPP：用于点对点链路的链路层协议
    - 帧中继：用于广域网连接的数据链路层协议
    - HDLC：一种同步数据链路层协议，用于点对点和多点链路
- L1 物理层
    - 以太网：IEEE 802.3 标准，规定了有线网络的物理传输方式
    - 无线局域网：IEEE 802.11 标准，定义了无线网络的物理层和数据链路层
    - USB：用于短距离设备连接的标准
    - 光纤通信标准：如 SONET/SDH，用于长距离高速光纤传输

## 网络通信

通信的两端可以有以下关系

- P2P（Peer-to-Peer）不区分客户端和服务端，两台主机是对等关系
- C/S（Client/Server）区分为客户端和服务端，客户端是主机上的程序
- B/S（Browser/Server）区分为客户端端和服务端，客户端是浏览器

无论服务端，还是客户端，运行的单位都是进程。

![20240616204339](https://image.zuoright.com/20240616204339.png)

一台机器上可以部署运行多个服务，比如同时开启了 SSH 服务和 HTTP 服务等。

一个客户端同一个时刻也可以建立多个到不同服务器的连接，比如同时购物、刷视频、看新闻等。

每个连接使用不同的端口来区分不同的服务和客户，服务端的端口是固定的，客户端是系统内核临时分配的，每个客户端每个连接的临时端口号都是不同的，确保数据不会混淆。

![20240616205722](https://image.zuoright.com/20240616205722.png)

四元组组成一个套接字对：`(clientaddr:clientport, serveraddr: serverport)`

### Socket

参考：

- <https://time.geekbang.org/column/article/113607>
- <https://mp.weixin.qq.com/s/fq5uyKXXp7HY8Clxf-AAbQ>

Socket 套接字，起初是由加州大学伯克利分校在 UNIX 的 BSD 分支提出的，所以也被叫做伯克利套接字，本身不是协议，只规定了 API，屏蔽了底层协议栈的差别，抽象出来的一层，可以理解为网络插槽。

![20240617175426](https://image.zuoright.com/20240617175426.png)

TCP 套接字通常叫做字节流套接字（SOCK_STREAM），UDP 套接字通常叫做数据报套接字（SOCK_DGRAM）

Socket 本质上就是个代码库，包含 `socket()`、`connect()`、`bind()` 等方法，这就是纯裸的 TCP 实现

![20240616212400](https://image.zuoright.com/20240616212400.png)

首先服务端初始化 socket，执行 bind 函数，将自己的服务能力绑定在一个众所周知的地址和端口上，紧接着，服务器端执行 listen 操作，将原先的 socket 转化为服务端的 socket，服务端最后阻塞在 accept 上等待客户端请求的到来。

服务器端准备就绪后。客户端根据 URL 获取服务端的地址和端口，即网络寻址或者服务发现。

然后客户端需要先初始化 socket，再执行 connect 向服务器端的地址和端口发起连接请求，即 TCP 三次握手。

然后客户端和服务器端建立连接，就进入了数据传输过程，可以进行 HTTP 请求传输数据等，然后需要和服务器端断开连接时，执行 close 函数。

### 传输方式

TCP 是一种基于字节流的协议，字节流是一种抽象，表示一系列连续的、没有明确边界的字节。

![20240612172103](https://image.zuoright.com/20240612172103.png)

应用程序可以随时发送任意长度的数据，TCP 将其视为一个连续的字节流，并负责将这些数据分成适合传输的段。

### 数据传输单元

消息在进入每一层时都会多加一个报头，报头上面记录着消息从哪来，到哪去，以及消息多长等信息

![20240617211209](https://image.zuoright.com/20240617211209.png)

- MTU, Maximum Transmission Unit 最大传输单元
- MSS, Maximum Segment Size 最大段长度

每一种链路层协议都规定了帧能传输的最大数据长度「MTU」，以太网的 MTU 一般默认为 1500 Bytes。

`MTU = MSS + TCP首部(20B) + IP首部(20B) + 可选部分`

![20230518083859](http://image.zuoright.com/20230518083859.png)

如果来自传输层的报文超过 MTU，IP 层则需要分段传输给 MAC 层。所以需要设置一个合适的段长度「MSS」，过大会导致 IP 分片传输，过小则网络利用率太低。

### 通信协议

不同协议诞生的顺序

![20240612165318](https://image.zuoright.com/20240612165318.png)

如果想将数据从一台电脑发送到另一台电脑，可以使用 socket 编程

但由于 tcp 是基于字节流传输的协议，无法区分数据边界，会有粘包问题

所以需要加一些协议，用于区分边界，于是基于 tcp 就衍生出了 rpc 和 http 等各种协议

rpc 本身并不是一个具体的协议，而是一种调用方式，即屏蔽掉一些细节，像调用本地方法一样调用远程方法，于是就出现了各种 rpc 协议，至今没有一个统一标准

> rpc 不一定都是依赖 tcp，也有 依赖 udp 或 http 的

起初在 C/S 架构中，客户端只需访问自家公司的服务端即可，所以使用各自的 rpc 协议就可以

但随着 B/S 浏览器的兴起，需要访问不同公司的服务器，因此需要一个统一的标准协议，不然无法通信，于是诞生了 http

也就是说 rpc 通常用于 C/S 架构，http 通常用于 B/S 架构，不过如今很多公司的服务端要同时支持移动端、PC 端以及 Web 端，所以通常都使用统一的 http 协议，而 rpc 开始退居幕后，主要用于公司内部集群中各个微服务之间的通信

- 服务发现

要向某个服务发起请求，需要先建立连接，也就需要先知道 IP 地址和端口，这个过程即服务发现

在 http 协议中，知道域名后，通过 DNS 服务解析即可获取到 IP，端口默认 80

rpc 协议中会有专门的中间服务保存服务名和IP信息，比如 consul 或 etcd，甚至是 redis，要想访问某个服务就需要先到这些中间服务获取 IP 和端口信息

- 底层连接

主流的 http 1.1 在建立底层 tcp 连接后，会一直保持连接，供之后的请求都复用，

rpc 协议跟 http 类似，也是建立 tcp 长连接进行数据交互，但不同的地方在于，rpc 协议一般还会再建个连接池，大大的提升了网络请求性能

> 很多编程语言的网络库也会给 http 加连接池来提高性能，比如 Go

- 序列化

数字和字符都可以通过编码直接转为二进制，而结构体则需要序列化

http 协议通常使用 json 来序列化结构体数据

rpc 不需要考虑重定向等浏览器行为，可以使用体积更小的序列化协议，不同的 rpc 可以根据自身特点定制化，比如 gRPC 使用 protobuf

因此 rpc 传输内容性能更好，这就是微服务之间采用 rpc 协议的主要原因

不过 http 2.0 改进提升了性能，比很多 rpc 协议还要好，比如 gRPC 底层就使用了 http 2.0

tcp 协议虽然是全双工的，但 http 协议是半双工的，同一时间只能一端给另一端发数据

比如扫码就是客户端请求服务端

- 轮询
- 长轮训

这是由于当时设计时未考虑像网页游戏这样的场景

于是基于 tcp 的全双工协议 websocket 应运而生

使用 websocket 协议需要先发送一个 http 协议，请求头中带上特殊的字段，然后升级为 ws 协议

正因如此，有人会说 websocket 是建立在 http 之上的，其实只是建立连接时用到了 http，升级到 ws 后便与 http 没有任何关系了

![20240612192609](https://image.zuoright.com/20240612192609.png)

websocket 完美继承了 tcp 的全双工能力，又解决了粘包问题，适用于客户端与服务器频繁交互的大部分场景，比如网页或小程序游戏、网页聊天室等

## 标准化组织

- IAB（Internet Architecture Board）互联网体系结构委员会，标准制定流程：首先在请求注释(RFC，Request For Comments)中描述整个思想，只有少数可以从互联网草案变为建议标准，最后变为互联网标准
  > IRTF（Internet Research Task Force）互联网研究任务组，专注长期的研究  
  > IETF（Internet Engineering Task Force）互联网工程任务组，专注短期的工程事项  
- ITU（International Telecommunication Union）国际电信联盟，联合国机构
  > ITU-T 电信标准化部门  
  > ITU-R 无线电通信部门  
  > ITU-D 发展部门  
- ISO（ISO，International Standards Organization）国际标准化组织，非条约性质的组织
- IEEE（Institute of Electrical and Electronics Engineers）电气和电子工程师协会，世界上最大的专业组织
- W3C（World Wide Web Consortium）万维网联盟，属于行业联盟，负责开发和给出促进Web长期增长的指导意见

## 参考

- 计算机网络·谢希仁
- [Computer Networks](https://book.douban.com/subject/1229951/)
- <https://en.wikipedia.org/wiki/Internet>
- [PowerCert Animated Videos](https://www.youtube.com/c/PowerCertAnimatedVideos/featured)
