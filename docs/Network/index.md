---
comments: true
hide:
  - feedback
---

# 引言

互联网 Internet（音译：因特网），通常认为，互联网起源于阿帕网（ARPANET）

常见的互联网服务有：万维网 WWW(World Wide Web)、文件传输 FTP、电子邮件 E-mail 等

## 网络分类

### 互联网

![20240620103939](https://image.zuoright.com/20240620103939.png)

大概 1970 年

![20240620104031](https://image.zuoright.com/20240620104031.png)

互联网使用 ISP 网络连接各种各样的网络

按照规模大小通常可分为三级：骨干网（Backbone Network）、地区网、校园网/企业网/小区网

![20240620104009](https://image.zuoright.com/20240620104009.png)

不同规模的 ISP 骨干网之间通过互联网交换点（IXP, Internet eXchange Point）连接交换数据，相互连接的 ISP 彼此是对等的（Peer），最顶级的 ISP 称之为 1 级 ISP。

![20240620104052](https://image.zuoright.com/20240620104052.png)

- 公用网，即电信公司建造的大型网络

```text
中国电信互联网 CHINANET  
中国联通互联网 UNINET  
中国移动互联网 CMNET  
中国教育和科研计算机网 CERNET  
中国科学技术网 CSTNNET
```

- 专用网，为满足某个部门或机构而建造的专用网络，比如军队、铁路、银行、电力等
- 星际互联网（Interplanetary Internet）：跨越太空把网络连接起来

![20230512153113](https://image.zuoright.com/20230512153113.png)

![20240625235956](https://image.zuoright.com/20240625235956.png)

Centralized(集中式), Decentralized(去中心化式), Distributed(分布式)

![中国互联网发展](https://image.zuoright.com/按用途划分.png)

```text
1994 亚马逊
1995 雅虎
1996 搜狐
1997 网易
1998 新浪、腾讯
1999 阿里巴巴
2000 百度
2004 Facebook
2005 YouTube
```

### 广域网

WAN, Wide Area Network

通常为跨地区或者国家通信，比如某公司分布式办公，不同地区的主机间通过路由器和通信线路通信，组成通信子网

网络服务由互联网服务提供商（ISP, Internet Service Provider）运营

![20210725150016](https://image.zuoright.com/20210725150016.png)

大 ISP 自己建造通信线路，小 ISP 则向电信公司租用通信线路

主机必须有IP才能上网，机构和个人向某个ISP交纳规定的费用，即可从该ISP获取所需IP地址的使用权，便可通过该ISP提供的子网接入到互联网

蜂窝/移动网络（Cellular / Mobile Network）是采用了无线技术的广域网

- 2G
  > GSM 全球移动通信系统  
  > GPRS 通用数据包无线业务  
- 3G
  > UMTS 通用移动通信系统：WCDMA（宽带码分多址）、CDMA（码分多址）  
  > HSPA  
- 4G: LTE、LTE-A  
- 5G: NR

接入点为基站，基站间通过有线的骨干网连接在一起

### 城域网

MAN, Metropolitan Area Network

![20230512231928](https://image.zuoright.com/20230512231928.png)

### 局域网

LAN, Local Area Network

- 有线局域网
  > 淘汰：令牌环、FDDI、ARCNET  
  > 现在：基于 IEEE 802.3 标准的以太网(Ethernet)  
- 虚拟局域网 VLAN(Virtual ~) 主要用于逻辑分组  
- 无线局域网 WLAN(Wireless ~)
  > 基于 IEEE 802.11 标准的 Wi-Fi  
- 存储网 SAN(Storage Area Network)，通常专用于服务器访问数据存储设备

无线设备通过无线路由器等接入点连接到有线网络

![20230512112432](https://image.zuoright.com/20230512112432.png)

交换机以特定的网络拓扑将不同的有线设备连接在一起组成有线局域网，即以太网，可以将物理局域网分成不同的逻辑局域网，即虚拟局域网

![20210725150926](https://image.zuoright.com/20210725150926.png)

### 个域网

PAN, Persinal Area Network

个人的电子设备间连接起来的网络，通常采用无线技术，所以也叫 WPAN（Wireless ～）

- 蓝牙
- USB
- 红外
- NFC

### 体域网

BAN, Body Area Network

主要指无线传感器网络

无线射频识别(RFID，Radio Frequency IDentification)技术则可以让日常物品也成为计算机网络的一部分

## 网络访问范围

### 明网

可以在正常浏览器搜索引擎搜到的网站

### 深网

如局域网或公司内网，需要先登录和身份验证才能访问

### 暗网

暗网中的网站和网页通过搜索引擎无法直接找到，即使知道暗网网站，普通的浏览器也无法浏览。

暗网的网址通常以 `.onion` 后缀结尾，`.onion` 不是顶级域名，但它是暗网里是唯一且通用的顶级后缀

访问暗网的浏览器

- PC 端：Tor
- 移动端：Orbot
- 搜索引擎：DuckDuckGo 暗网版

## 网络分层

阿帕网使用网络控制协议（Network Control Protocol, NCP）来连接不同的计算机进行通信，1974 年 NCP 的两位开发者发表了以分组、序列化、流量控制、超时和容错等为核心的一种新型网络互联协议套件（IPS）于 1983 年正式取代 NCP，即后来的 TCP/IP 协议簇。

在 TCP/IP 协议的基础之上，1984 年 ISO 又推出了开放式系统互联模型：OSI(Open System Interconnection)。

OSI 总共分为七层，每一层明确了编号，描述的网络更加完整。

但无论 TCP/IP 还是 OSI 都只是参考模型，并不是标准，无论是四层、五层、还是七层，本质都是一样的。

![20240618001433](https://image.zuoright.com/20240618001433.png)

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

## Socket

参考：

- <https://time.geekbang.org/column/article/113607>
- <https://mp.weixin.qq.com/s/fq5uyKXXp7HY8Clxf-AAbQ>

Socket 套接字，起初是由加州大学伯克利分校在 UNIX 的 BSD 分支提出的，所以也被叫做伯克利套接字，本身不是协议，只规定了 API，屏蔽了底层协议栈的差别，抽象出来的一层，可以理解为网络插槽。

![20240617175426](https://image.zuoright.com/20240617175426.png)

- TCP 套接字通常叫做字节流套接字（SOCK_STREAM）
- UDP 套接字通常叫做数据报套接字（SOCK_DGRAM

Socket 本质上就是个代码库，包含 `socket()`、`connect()`、`bind()` 等方法，这就是纯裸的 TCP 实现

![20240616212400](https://image.zuoright.com/20240616212400.png)

首先服务端初始化 socket，执行 bind 函数，将自己的服务能力绑定在一个众所周知的地址和端口上，紧接着，服务器端执行 listen 操作，将原先的 socket 转化为服务端的 socket，服务端最后阻塞在 accept 上等待客户端请求的到来。

然后客户端需要先初始化 socket，再执行 connect 向服务器端的地址和端口发起连接请求，客户端可以通过 DNS 服务解析出 URL 对应的地址和端口，即服务发现。

三次握手后客户端和服务器端建立连接，就进入了数据传输过程，可以进行 HTTP 请求传输数据等，然后需要和服务器端断开连接时，执行 close 函数。

## 传输方式

TCP 是一种基于字节流的协议，字节流是一种抽象，表示一系列连续的、没有明确边界的字节。

![20240612172103](https://image.zuoright.com/20240612172103.png)

应用程序可以随时发送任意长度的数据，TCP 将其视为一个连续的字节流，并负责将这些数据分成适合传输的段。

## 数据传输单元

消息在进入每一层时都会多加一个报头，报头上面记录着消息从哪来，到哪去，以及消息多长等信息

![20240617211209](https://image.zuoright.com/20240617211209.png)

- MTU, Maximum Transmission Unit 最大传输单元
- MSS, Maximum Segment Size 最大段长度

每一种链路层协议都规定了帧能传输的最大数据长度「MTU」，以太网的 MTU 一般默认为 1500 Bytes。

`MTU = MSS + TCP首部(20B) + IP首部(20B) + 可选部分`

![20230518083859](https://image.zuoright.com/20230518083859.png)

如果来自传输层的报文超过 MTU，IP 层则需要分段传输给 MAC 层。所以需要设置一个合适的段长度「MSS」，过大会导致 IP 分片传输，过小则网络利用率太低。

## 传输标识

### URI

Uniform Resource Identifier 统一资源标识符，是一种抽象的定义，只要能唯一标识资源的都可以叫 URI

- URN(Uniform Resource Name) 统一资源名称，通过名称定位标识资源，比如书籍的 isbn 号
- URL(Uniform Resource Locator) 统一资源定位符，通过地址定位标识资源，最常用的就是网址

URL 组成：`scheme:subscheme://username:password@host:port/path?query#fragment`

- scheme 协议，比如：https, ftp 等
- subscheme 子协议，用于区分数据库

比如：`jdbc:mysql://localhost:3306/...`

- host 域名 或 IP

格式：<四级域名.三级域名.二级域名.顶级域名>，比如 <www.apple.com.cn>

![20240616210727](https://image.zuoright.com/20240616210727.png)

- port 服务端端口号

服务端端口通常是固定的，SMTP 默认 25，HTTP 默认 80，HTTPS 默认 443，Django 默认 8000

- resources 资源
    - path 带层次的文件路径，`/虚拟目录/文件名`
    - `?query` 查询字符串，传入参数
    - `#fragment` 片段标识符，比如定位文档内具体位置

资源就是网络上的一个实体，可以有不同的表现形式，称之为表现层。它可以是文本（TXT、XML、JSON、HTML）、图片（PNG、JPG）、音频、视频、服务等等

可以在请求头中使用 `Accept` 和 `Content-Type` 字段指定表现形式以及版本号等

### 端口号

每个连接使用不同的端口来区分不同的服务和客户，服务端的端口是固定的，客户端是系统内核临时分配的，每个客户端每个连接的临时端口号都是不同的，确保数据不会混淆。

![20240616205722](https://image.zuoright.com/20240616205722.png)

IP 和 Port 组成一个四元组，也叫套接字对：`(clientaddr:clientport, serveraddr: serverport)`

### IP 地址

分配而来

类似于办公地址，有定位功能

- `localhost`

不是 IP，而是域名，用于指代当前 host 或 computer，通常被指向 IPv4 的 `127.0.0.1` 和 IPv6 的 `::1`

- `127.0.0.1`

凡是以 127 开头的地址，都属于回环地址(loop back address)，即发送给此地址的请求会被自己接收，外部设备无法访问

127.0.0.1 只是回环地址中的一个，可用于测试网络是否正常：`ping 127.0.0.1`

如果遇到 DDos 攻击，可以将域名 A 记录解析到 127.0.0.1，让其攻击自己

- `0.0.0.0`

并不是一个真实的的 IP 地址，无意义或无效的地址

通常用于不便使用具体地址的特殊情况，可表示本机中所有的 IPv4 地址

### Mac 地址

每块网卡出厂时都有的唯一标识，类似于 身份证号，无定位功能

地址解析协议

- ARP：已知 IP 求 MAC
- RARP：已知 MAC 求 IP

## DNS

![20240613153610](https://image.zuoright.com/20240613153610.png)

Domain Name System 域名系统，是域名与 IP 地址相互映射的一个分布式数据库，因为 IP 地址不方便记忆，使用域名来映射它，比如 `localhost` 对应的是 `127.0.0.1`

域名地址有不同的记录类型，每种类型可以包含多条记录。

```plaintext
A, Address Record 将域名映射到一个IPv4地址，最常用
CNAME, Canonical Name 将域名指向另一个域名，而不是 IP，根域名不能设置为 CNAME
MX, Mail Exchange Record 指向处理邮件的服务器地址
```

浏览器通过 URL 解析出域名后

- 首先会查询本地 DNS 缓存是否有该域名的地址记录

先查看浏览器自身的 DNS 缓存 <chrome://net-internals/#dns>

没有再查找操作系统的 DNS 缓存文件 `/etc/hosts`

本地缓存通常包含以下信息

```text
域名
IP地址
TTL（Time to Live）有效期限
```

- 本地如果没有就会查询 DHCP 下发的 DNS 服务器

通常由 DHCP 服务器（路由器）自动分配，或者在拨号时从 PPP 服务器中自动获取，也可以用户手动指定

其实就是 DNS 服务器的缓存

- DNS 服务器进行递归查询：根域名服务器、顶级域名服务器、权威域名服务器

权威域名服务器根据访问者所处的不同地区（如华北、华南、东北等）、不同服务商（如电信、移动、联通等）等因素智能选择出某种记录类型对应的最合适的记录，返回给客户端

如果域名服务器挂了，或域名服务器的 IP 填错了，或请求被 GFW 拦截了，最终都会没有响应然后报错

基于 UDP 的 DNS 分级查询意味着每一级都有可能受到中间人攻击的威胁，比如域名劫持、域名污染/欺骗

可以使用安全的DNS协议来解决，比如 DoH（[DNS over HTTPS](https://en.wikipedia.org/wiki/DNS_over_HTTPS)）

## 防火墙

- 硬件防火墙：防御（比如DDS攻击），兼顾软件数据包过滤功能
- 软件防火墙：数据包过滤，是否允许某个 IP 或者端口进入主机或者转发等，又可以分为包过滤防火墙和应用防火墙

从 Linux Kernel 2.4 版开始，内核开放了一套通用的、可供代码干预数据在协议栈中流转的过滤器框架 Netfilter，基于 Netfilter 设计的 Xtables 系工具，比如 `iptables`。

iptables 的价值便是以配置去实现原本用 Netfilter 编码才能做到的事情。

- 四(规则)表：`filter`、`nat`、`mangle`、`raw`
- 五(规则)链：`INPUT`、`OUTPUT`、`FORWARD`、`PREROUTING`、`POSTROUTING`

## 标准

### 类型

- 草案
- 建议标准
- 事实标准
- 法定标准

征求意见稿（RFCs）：互联网的设计文档

### 组织

- W3C（World Wide Web Consortium）万维网联盟，属于行业联盟，负责开发和给出促进 Web 长期增长的指导意见
- ISO（ISO，International Standards Organization）国际标准化组织，非条约性质的组织，总部位于瑞士日内瓦，成员包括 162 个会员国。
- IEEE（Institute of Electrical and Electronics Engineers）电气和电子工程师协会，世界上最大的专业组织
- ICANN：互联网名称与数字地址分配机构，负责管理域名和IP地址的分配
- IAB（Internet Architecture Board）互联网体系结构委员会，标准制定流程：首先在请求注释(RFC，Request For Comments)中描述整个思想，只有少数可以从互联网草案变为建议标准，最后变为互联网标准
    - IRTF（Internet Research Task Force）互联网研究任务组，专注长期的研究  
    - IETF（Internet Engineering Task Force）互联网工程任务组，专注短期的工程事项  
- ITU（International Telecommunication Union）国际电信联盟，联合国机构
    - ITU-T 电信标准化部门
    - ITU-R 无线电通信部门
    - ITU-D 发展部门

## 参考

- 计算机网络·谢希仁
- [Computer Networks](https://book.douban.com/subject/1229951/)
- <https://en.wikipedia.org/wiki/Internet>
- [PowerCert Animated Videos](https://www.youtube.com/c/PowerCertAnimatedVideos/featured)
