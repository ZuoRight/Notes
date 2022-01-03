# Internet

因特网(音译)，即互联网，主要提供了以下服务：

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

## 参考

> 主要参考自Youtube UP主 [PowerCert Animated Videos](https://www.youtube.com/c/PowerCertAnimatedVideos/featured)的动画视频

- [网络分类](https://www.bilibili.com/video/BV1Lb411C7Hf)
  > PAN(个人局域网)：USB、蓝牙、红外、NFC  
  > CAN(校园局域网)  
  > SAN(数据存储专用网络)
- [CSMA/CD和CSMA/CA](https://www.bilibili.com/video/BV1gb411C7HX)
- [集线器，交换机，路由器](https://www.bilibili.com/video/BV1yt411d7Rd)
  > 集线器：仅检测设备与其物理设备，用于本地交换数据  
  > 交换机：可读取MAC地址，可检测特定设备，用于本地交换数据  
  > 路由器：可读取IP地址，用于外网交换数据，集成多张网卡，本质上是一个网关

网关 = 路由器（基础职能） + 过滤器（可选职能）
