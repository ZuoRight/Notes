# 引言

## 信道 Channel

信道，即传送信息的通道

- 物理信道
  > 有线信道：双绞线、同轴电缆、光纤等  
  > 无线信道：微波通信、电台广播、卫星通信等  
  > 存储信道：通过硬盘等存储介质从某处向某处转移数据
- 逻辑信道：抽象的信道

### 信道的工作模式

- 单工(simplex)：只能单向传输，比如电台广播，信号只能从电台发给收音机，而收音机不能向电台发送信号
- 半双工(half-duplex)：可以双向，但不能同时传输，比如独木桥、单轨铁道
- 全双工(full-duplex)：可以双向同时传输，互不干扰，比如光纤

### 多路复用

- 频分多路复用FDM(Frequency-Division Multiplexing)：不同线路占用不同的频率，比如电台广播
- 时分多路复用TDM(Time-Division Multiplexing)：类似十字路口红绿灯
- 码分多路复用CDM(Code-Division Multiplexing)：线路间采用不同的编码方式使得端点可以在同一时间同一频段发送数据，CDMA(码分多址)就是这个思路

## 通信模式

当两个以上通信端点（可以是网卡等硬件，也可以是软件程序）可构成一个网络，端点间通信会涉及如下几种方式

- 广播(broadcast)：发给所有端点
- 组播(multicast)：发给指定的多个端点
- 单播(unicast)：发给指定的单个端点
- 选播(anycast)：发给随机的单个端点

## 网络架构模式

主机A的某个进程和主机B上的另一个进程进行通信

- P2P（Peer-to-Peer）不区分客户端和服务端，两台主机是对等关系
- C/S（Client/Server）区分为客户端和服务端，客户端是主机上的程序
- B/S（Browser/Server）区分为客户端端和服务端，客户端是浏览器

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

- [《Computer Networks》](https://book.douban.com/subject/1229951/)
- <https://en.wikipedia.org/wiki/Internet>
- [PowerCert Animated Videos](https://www.youtube.com/c/PowerCertAnimatedVideos/featured)
