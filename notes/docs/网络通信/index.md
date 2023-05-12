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

### 带宽

带宽，指信道在单位时间内传输多少比特的信息

- 电气领域：指的是模拟带宽，单位：Hz(赫兹)
- 计算机领域：指的是数字带宽，单位：bps(比特率) 或 Bps(字节率)
  > 十进制：用K、M、G等表示，1K=1000，比如1Kbps，表示1000比特每秒  
  > 二进制：用Ki、Mi、Gi等表示，1Ki=1024，比如1KiBps，表示1024字节每秒

带宽的限制：信噪比，信道传输的有用信息与无用的干扰噪声的比值，单位为分贝(dB)

### 多路复用

- 频分多路复用FDM(Frequency-Division Multiplexing)：不同线路占用不同的频率，比如电台广播
- 时分多路复用TDM(Time-Division Multiplexing)：类似十字路口红绿灯
- 码分多路复用CDM(Code-Division Multiplexing)：线路间采用不同的编码方式使得端点可以在同一时间同一频段发送数据，CDMA(码分多址)就是这个思路

## 通信方式

当两个以上通信端点（可以是网卡等硬件，也可以是软件程序）可构成一个网络，端点间通信会涉及如下几种方式

- 广播(broadcast)：发给所有端点
- 组播(multicast)：发给指定的多个端点
- 单播(unicast)：发给指定的单个端点
- 选播(anycast)：发给随机的单个端点

## 标准化组织

- ISO
- IEEE
- ITU-T
- IAB

## 参考

- [《Computer Networks》](https://book.douban.com/subject/1229951/)
- <https://en.wikipedia.org/wiki/Internet>
- [PowerCert Animated Videos](https://www.youtube.com/c/PowerCertAnimatedVideos/featured)
