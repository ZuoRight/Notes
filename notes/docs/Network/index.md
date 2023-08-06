# 引言

## 网络分类

### 体域网

BAN, Body Area Network

主要指无线传感器网络

无线射频识别(RFID，Radio Frequency IDentification)技术则可以让日常物品也成为计算机网络的一部分

### 个域网

PAN, Persinal Area Network

个人的电子设备间连接起来的网络，通常采用无线技术，所以也叫 WPAN（Wireless ～）

- 蓝牙
- USB
- 红外
- NFC

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

![20230512112432](http://image.zuoright.com/20230512112432.png)

交换机以特定的网络拓扑将不同的有线设备连接在一起组成有线局域网，即以太网，可以将物理局域网分成不同的逻辑局域网，即虚拟局域网

![20210725150926](http://image.zuoright.com/20210725150926.png)

### 城域网

MAN, Metropolitan Area Network

![20230512231928](http://image.zuoright.com/20230512231928.png)

### 广域网

WAN, Wide Area Network

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
