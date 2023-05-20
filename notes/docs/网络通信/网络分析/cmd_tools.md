# 命令行工具

## 数据链路层

arp

```shell
# 查看ARP Cache
arp -a  # windows

# 动态类型是通过广播获取然后缓存的
# 否则就是静态的，比如手动添加
arp -s IP MAC
```

![20210729233402](http://image.zuoright.com/20210729233402.png)

## 网络层

### ipconfig

```shell
# windows
ipconfig /all

# linux/mac
ifconfig  # 需要安装net-tools，已停止维护
ip addr  # 需要安装iproute2，逐渐取代net-tools，推荐
```

返回字段解析

```shell
# 第一行
eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>  # net_device flags(网络设备状态标识)
    # BROADCAST 表示这个网卡有广播地址，可以发送广播包
    # MULTICAST 表示网卡可以发送多播包
    # UP 表示网卡处于启动的状态
    # LOWER_UP 表示L1是启动的，也即网线插着呢
mtu 1500  # 最大传输单元1500字节(这是以太网的默认值)
    # MAC头后面的正文(包括IP头、TCP头、HTTP头)不允许超过1500个字节，存不下则分片传输
qdisc pfifo_fast  # queueing discipline(排队规则)
    # 内核通过某个网络接口发送数据包时，需要按照这个排队规则将数据包加入队列
state UP

# 第二行
link/ether 00:15:5d:21:b9:ea brd ff:ff:ff:ff:ff:ff  # mac地址

# 第三行
xxx.xx.xxx.x scope global eth0  # ethernet 以太网卡，范围global，对外访问
127.0.0.1/8 scope host lo  # loopback 环回接口，范围host，供本机相互通信
```

### ping

使用ICMP协议测试某个远程主机是否可达

不可达的原因

- IP对应的主机关机
- IP对应的主机断线
- IP对应的主机拒绝响应ICMP协议
- 本机到IP对应主机之间有防火墙拦截了ICMP协议

### traceroute

测试本机到目标主机经过哪些路由

## 传输层

### netcat

### ss

### nmap

开源的扫描器

端口扫描是其功能之一，扫描远程主机监听了哪些传输层端口
