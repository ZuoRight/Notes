---
comments: true
hide:
  - feedback
---

# 引言

互联网 Internet（音译：因特网），通常认为，互联网起源于阿帕网（ARPANET）

常见的互联网服务有：万维网 WWW(World Wide Web)、文件传输 FTP、电子邮件 E-mail 等

万维网（WWW）是由很多链接在一起的文档和资源构成的，为此万维网联盟（W3C）制定了一系列标准，比如

- 结构标准语言：XML、HTML
- 行为标准语言：DOM、ECMAScript
- 表现标准语言：CSS

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

- 有线局域网

LAN, Local Area Network，一般讨论局域网时，通常就是指以太网

```text
淘汰的：令牌环、FDDI、ARCNET  
现在的：基于 IEEE 802.3 标准的以太网(Ethernet)
```

![20230518112135](https://image.zuoright.com/20230518112135.png)

```text
总线型拓扑，主要用于早期传统以太网
所有节点通过一条主干线直接连接，数据在主干线上传输，故障诊断困难，主干线故障会导致整个网络瘫痪
不能同时发送和接收，属于半双工通信，使用 CSMA/CD（载波监听多点接入/碰撞检测）来控制

星型拓扑，主要用于现在快速以太网（100Mbps 以上）
所有节点通过独立的连接线连接到一个中央节点（如交换机或集线器）
如果中央节点是集线器，逻辑上仍是总线网，各主机中的适配器依然使用 CSMA/CD 协议来竞争对传输媒介的控制
如果中央节点是交换机，则变为全双工通信，不需要 CSMA/CD

网状拓扑，通常用于家庭网络、无线网络
每个节点都与网络中的其他节点直接连接，不仅可以作为客户端设备的接入点，还可以中继数据，确保全网的连接和通信
高可靠性和冗余，故障节点不会影响整个网络
```

![20210725191255](https://image.zuoright.com/20210725191255.png)

- 虚拟局域网

可以将物理局域网进行逻辑分组，即虚拟局域网（VLAN, Virtual LAN）

VLAN 是局域网给用户提供的一种服务，由一些局域网网段构成的与物理位置无关的逻辑组，相同 VLAN 上的主机可以收到彼此的广播信息

![20230518190950](https://image.zuoright.com/20230518190950.png)

- 无线局域网 WLAN(Wireless ~)

基于 IEEE 802.11 标准的 Wi-Fi

使用 CSMA/CA（载波监听多点接入/碰撞避免）协议，可选择与 RTS/CTS 协议搭配使用

无线设备通过无线路由器等接入点连接到有线网络

![20230512112432](https://image.zuoright.com/20230512112432.png)

- 存储网 SAN(Storage Area Network)，通常专用于服务器访问数据存储设备

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

### L7 应用层

提供网络服务的接口，直接与用户应用程序交互

```text
HTTP：用于Web服务的半双工传输协议
WebSocket：用于Web服务的全双工传输协议
FTP：用于文件传输
SMTP：用于电子邮件传输
DNS：将域名解析为IP地址
POP3/IMAP：用于电子邮件的接收
SSH：用于安全的远程登录
Telnet：用于远程控制协议，但安全性较差
```

### L6 表示层

负责数据的格式化、加密和解密，提供数据表示的独立性

```text
SSL/TLS：提供数据加密和安全通信
JPEG、GIF：用于图像数据的格式化和压缩
MIME：用于邮件附件的格式化
ASCII、EBCDIC：用于文本数据的编码格式
```

### L5 会话层

不同机器上的用户之间建立及管理会话：RPC、SQL、NFS

```text
NetBIOS：用于会话层连接和数据传输的API
RPC：允许程序在网络上的另一台计算机上执行代码
PPTP：用于虚拟专用网（VPN）的协议
```

### L4 传输层

屏蔽有无连接的差异，面向进程：TCP、UDP、SPX

```text
TCP：提供可靠的、面向连接的数据传输
UDP：提供无连接的、不可靠的数据传输，适用于实时应用
SCTP：用于多流的数据传输，提供更高级的传输控制
```

### L3 网络层

IP、ARP

```text
IP：IPv4 和 IPv6，负责数据包的寻址和路由
ICMP：用于传输控制消息，如ping命令
ARP：将IP地址转换为MAC地址
NAT：转换私有IP地址和公共IP地址
OSPF：一种链路状态路由协议
BGP：一种自治系统间的路由协议
```

### L2 数据链路层

链路(link)：从一个结点到相邻结点的一段物理路线（有线或无线）

数据链路(data link)，也叫逻辑链路：链路 + 网络适配器（网卡）

数据链路层需要处理的问题

```text
信息打包，需要将物理层传来的比特流转为帧
差错控制，受噪声干扰物理层传输可能会出现误码率（BER, Bit Error Rate），即在一段时间内，`BER = 传输错误的比特 / 所传输的比特总数`，传输错误的比特指 1 变为了 0，或 0 变为了 1，数据链路层通常使用循环冗余检验（CRC, Cyclic Redundancy Check）等检错技术，无线信道中通常可能需要纠错算法来检错的同时还能纠错
流量控制，信道两端处理信息的速度可能不同，需要某种机制来协调
信道多路复用，需要 MAC 协议控制
```

涉及协议

```text
Ethernet：IEEE 802.3，管理 MAC 地址及以太网帧的传输
Wi-Fi：IEEE 802.11，管理无线数据帧传输和MAC地址
PPP：用于点对点链路的链路层协议
帧中继：用于广域网连接的数据链路层协议
HDLC：一种同步数据链路层协议，用于点对点和多点链路
```

### L1 物理层

物理层的主要任务就是确定与传输媒介的接口有关的一些特性，如机械特性、电气特性、功能特性和过程特性

```text
以太网：IEEE 802.3 标准，规定了有线网络的物理传输方式
无线局域网：IEEE 802.11 标准，定义了无线网络的物理层和数据链路层
USB：用于短距离设备连接的标准
光纤通信标准：如 SONET/SDH，用于长距离高速光纤传输
```

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
