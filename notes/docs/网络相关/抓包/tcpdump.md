# tcpdump

`tcpdump -n -i en0 -s 0 -w demo.pcap host 220.181.38.251`

- `-n`  直接显示IP，而不是把IP转化为域名，速度会快很多
- `-i en0`  指定网卡
- `-s 0`  0表示截取全部报文（默认截取前96字节）
- `-w demo.pcap`  将过滤结果输出到`.pcap`文件，然后借助WireShark等带界面的抓包工具二次分析
- `host ip`  指定IP

![20210809185523](http://image.zuoright.com/20210809185523.png)

参考：[明哥·全网最全tcpdump抓包指南](https://iswbm.com/70.html#9)
