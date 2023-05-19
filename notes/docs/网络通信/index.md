# 引言

## 网络

### BAN

Body Area Network 体域网

主要指无线传感器网络

无线射频识别(RFID，Radio Frequency IDentification)技术则可以让日常物品也成为计算机网络的一部分

### PAN

Persinal Area Network 个域网

个人的电子设备间连接起来的网络，通常采用无线技术，所以也叫 WPAN（Wireless ～）

- 蓝牙
- USB
- 红外
- NFC

### LAN

Local Area Network 局域网

- 有线局域网
  > 淘汰：令牌环、FDDI、ARCNET  
  > 现在：基于 IEEE 802.3 标准的以太网(Ethernet)  
- 虚拟局域网 VLAN(Virtual ~) 主要用于逻辑分组  
- 无线局域网 WLAN(Wireless ~)
  > 基于 IEEE 802.11 标准的 Wi-Fi  
- 存储网 SAN(Storage Area Network)，通常专用于服务器访问数据存储设备

无线设备通过无线路由器等接入点连接到有线网络

![20230512112432](http://image.zuoright.com/20230512112432.png)

交换机以特定的网络拓扑将不同的有线设备连接在一起组成有线局域网，即以太网，可以将物理局域网分成不同的逻辑局域网，即虚拟局域网

![20210725150926](http://image.zuoright.com/20210725150926.png)

### MAN

Metropolitan Area Network 城域网

![20230512231928](http://image.zuoright.com/20230512231928.png)

### WAN

Wide Area Network 广域网

通常为跨地区或者国家通信，比如某公司分布式办公，不同地区的主机间通过路由器和通信线路通信，组成通信子网

网络服务由互联网服务提供商（ISP, Internet Service Provider）运营

![20210725150016](http://image.zuoright.com/20210725150016.png)

大ISP自己建造通信线路，小ISP则向电信公司租用通信线路

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

### 互联网

Internet 音译：因特网，即互联网

互联网使用ISP网络连接各种各样的网络，即网络的网络。

按照规模大小通常可分为三级：骨干网（Backbone Network）、地区网、校园网/企业网/小区网

不同规模的ISP骨干网之间通过互联网交换点（IXP, Internet eXchange Point）连接交换数据，相互连接的ISP彼此是对等的（Peer），最顶级的ISP称之为1级ISP。

![20230512153113](http://image.zuoright.com/20230512153113.png)

按用途划分

- 公用网，即电信公司建造的大型网络
  > 中国电信互联网 CHINANET  
  > 中国联通互联网 UNINET  
  > 中国移动互联网 CMNET  
  > 中国教育和科研计算机网 CERNET  
  > 中国科学技术网 CSTNNET
- 专用网，为满足某个部门或机构而建造的专用网络，比如军队、铁路、银行、电力等

### 星际互联网

Interplanetary Internet

跨越太空把网络连接起来

## 协议参考模型

互联网早期，为了把不同的网络链接起来，1983年推出了互联网协议套件(IPS)：TCP/IP

在TCP/IP协议的基础之上，1984年ISO又推出了开放式系统互联模型：OSI(Open System Interconnection)

OSI 总共分为七层，每一层明确了编号，描述的网络更加完整

无论 TCP/IP 还是 OSI 都只是参考模型，并不是标准，无论是四层、五层、还是七层，本质都是一样的

- 应用层｜业务层：L7～L5
  > DNS  
  > HTTP  
  > SMTP
- 运输层｜传输层：L4
  > TCP  
  > UDP
- 网络层｜网际层｜IP层：L3
  > IP
- 网络接口层：L2～L1

![20230515220926](http://image.zuoright.com/20230515220926.png)

![网络分层模型](http://image.zuoright.com/网络分层模型.png)

![20210808231312](http://image.zuoright.com/20210808231312.png)

![20220103224646](http://image.zuoright.com/20220103224646.png)

每一种链路层协议都规定了帧能传输的最大数据长度，即最大传输单元（MTU, Maximum Transmission Unit）

![20230518083859](http://image.zuoright.com/20230518083859.png)

以太网MTU一般默认为1500 Bytes，如果来自传输层的报文超过MTU，IP层则需要分段传输给MAC层。所以需要设置一个合适的段长度，称之为最大段长度（MSS, Maximum Segment Size），过大会导致IP分片传输，过小则网络利用率太低。

`MTU = MSS + TCP首部(20B) + IP首部(20B) + 可选部分`

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

常见的拓扑结构（network topology）

![20210725170757](http://image.zuoright.com/20210725170757.png)
