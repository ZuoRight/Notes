# 引言

互联网早期，为了把不同的网络链接起来，1983年推出了互联网协议套件(IPS)：TCP/IP

在TCP/IP协议的基础之上，1984年ISO又推出了开放式系统互联模型：OSI(Open System Interconnection)

OSI 总共分为七层，每一层明确了编号，描述的网络更加完整

无论 TCP/IP 还是 OSI 都只是参考模型，并不是标准，无论是四层、五层、还是七层，本质都是一样的

- L7 应用层：HTTP、SMTP、FTP、DNS、WebSocket
- L6 表示层，加密解密、转换翻译、压缩解压缩：JPEG、GIF
- L5 会话层，不同机器上的用户之间建立及管理会话：RPC、SQL、NFS
- L4 传输层，屏蔽有无连接的差异，面向进程：TCP、UDP、SPX
- L3 网际层，屏蔽异种网络路由的差异，面向主机：IP、ARP
- L2 数据链路层
- L1 物理层

上三层与软件相关，可以统称为业务层，传输层承上启下，下三层与硬件相关

![20230520220837](http://image.zuoright.com/20230520220837.png)

![网络分层模型](http://image.zuoright.com/网络分层模型.png)

![20210808231312](http://image.zuoright.com/20210808231312.png)

![20230823204412](https://image.zuoright.com/20230823204412.png)

![20220103224646](http://image.zuoright.com/20220103224646.png)

每一种链路层协议都规定了帧能传输的最大数据长度，即最大传输单元（MTU, Maximum Transmission Unit）

![20230518083859](http://image.zuoright.com/20230518083859.png)

以太网MTU一般默认为1500 Bytes，如果来自传输层的报文超过MTU，IP层则需要分段传输给MAC层。所以需要设置一个合适的段长度，称之为最大段长度（MSS, Maximum Segment Size），过大会导致IP分片传输，过小则网络利用率太低。

`MTU = MSS + TCP首部(20B) + IP首部(20B) + 可选部分`
