# Internet

音译因特网，即互联网，主要提供了以下服务：

- 文件传输 FTP
- 电子邮件 E-mail
- 远程登录 Telnet
- 万维网 WWW

这些服务所依赖的底层技术主要有：

- 已被取代：令牌环、FDDI、ARCNET
- 目前最广泛：以太网

## 以太网

以太网用交换机以特定的网络拓扑(Network topology)结构将不同的设备连接在一起组成局域网

> 有线局域网(LAN)、WLAN(无线局域网)、VLAN(虚拟局域网)

![20210725150926](http://image.zuoright.com/20210725150926.png)

ISP(互联网服务提供商)通过路由器把局域网连接起来就形成城域网(MAN)等

![20210725150016](http://image.zuoright.com/20210725150016.png)

在重要城市以及不同区域架设骨干服务器和分支服务器把不同城市连接起来，即构成了骨干网

![20210725144721](http://image.zuoright.com/20210725144721.png)

于是就构建和连接出了广域网(WAN)，把全世界的广域网连接到一起则衍变成了因特网

![20210725173808](http://image.zuoright.com/20210725173808.png)

## 网络协议

TCP/IP四层模型(IPS，1983)和OSI(开放式系统互联通信参考模型，1984)七层模型相继推出，它们都只是参考模型，并不是标准。映射关系如下

![20210725235111](http://image.zuoright.com/20210725235111.png)

OSI中的前两层主要由以太网定义

![20210725192635](http://image.zuoright.com/20210725192635.png)

### 物理层

![20210725191255](http://image.zuoright.com/20210725191255.png)

早期以太网使用总线型拓扑(Bus，下图右)，通过同轴电缆和集线器(Hub)连接网络（半双工，不能同时双向传输数据，每次只能单向传输数据）。

目前的快速以太网(100Mbps以上)实际所使用的主要是星型拓扑(Star，下图左)。通过双绞线和玻璃/塑料光纤线缆等，以及路由器和交换机(SWITCH)来连接设备（全双工，可同时双向传输数据）。

### 数据链路层

- 逻辑链路控制(LLC)：为网络提供统一的接口，以便数据在设备之间传输
- 介质访问控制(MAC)：分配网卡地址，以标识网络设备

---

- CSMA/CA(载波监听多点接入/碰撞避免)：主要应用于早期以太网半双工有线通信中
- CSMA/CD(载波监听多点接入/碰撞检测)：主要应用于无线网络中，可选择与RTS/CTS协议搭配使用

## 参考

> 主要参考自B站某UP主，翻译自Youtube UP主 [PowerCert Animated Videos](https://www.youtube.com/c/PowerCertAnimatedVideos/featured)的动画视频
> 
> 有条件的可以直接Youtube看（自带翻译）

- [CSMA/CD和CSMA/CA](https://www.bilibili.com/video/BV1gb411C7HX)
- [集线器，交换机，路由器](https://www.bilibili.com/video/BV1yt411d7Rd)
  > 集线器：仅检测设备与其物理设备，用于本地交换数据  
  > 交换机：可读取MAC地址，可检测特定设备，用于本地交换数据  
  > 路由器：可读取IP地址，用于外网交换数据，集成多张网卡，本质上是一个网关
- [网络分类](https://www.bilibili.com/video/BV1Lb411C7Hf)
  > PAN(个人局域网)：USB、蓝牙、红外、NFC  
  > CAN(校园局域网)  
  > SAN(数据存储专用网络)
