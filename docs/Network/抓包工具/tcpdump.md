# tcpdump

![20210809185523](http://image.zuoright.com/20210809185523.png)

参考：[全网最全tcpdump抓包指南](https://iswbm.com/70.html#9)

```shell
tcpdump -n -i en0 -s 0 -w demo.pcap host 220.181.38.251
'
src host ip 指定IP
-i en0 指定网卡
-n 直接显示IP，而不是域名，速度会快很多
-s 0 0 表示截取全部报文（默认截取前96字节）
-w demo.pcap 将过滤结果输出到 .pcap 文件，然后借助 WireShark 等带界面的抓包工具二次分析
'

# 抓包：TCP 协议，80 端口，来自某IP
tcpdump 'tcp and port 80 and src host 192.168.1.25'
```
