# 引言

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

- MSS(Maximum Segment Size) 最大段长度
- MTU(Maximum Transmission Unit) 最大传输单元

`MTU = MSS + TCP首部(20B) + IP首部(20B) + 可选部分`

以太网MTU一般默认为1500 Bytes，如果来自传输层的报文超过MTU，IP层则需要分片传输给MAC层。据此，需要设置一个合适的MSS，过大会导致IP分片传输，过小则网络利用率太低。

## 交换

![20230515000832](http://image.zuoright.com/20230515000832.png)

存储转发（Store and forward）建模自邮政系统，也类似于物流系统，即信件/货物/信息被发送到中间站，查找转发表继续转发到下一站

### 电路交换

所有的电话两两相连是不现实的，而是通过低速线路连接到交换机，多个低层交换机再连到更上层的交换机，然后连到交换局，电话交换局之间通过高速线路连接，这些线路和交换机设备组成了一个个子网，最后形成电信网，由电话公司负责管理，而电话本身并不属于子网。

![20230514235241](http://image.zuoright.com/20230514235241.png)

### 报文交换

需要存储全部的报文才会转发

### 分组交换

报文被分割为一个个分组，每个分组都是一个数据包，由首部和数据两部分组成

路由器存储转发时，不需要等待完整的报文，分组转发即可。

## 服务

![20230513144032](http://image.zuoright.com/20230513144032.png)

### 面向连接的服务

connection-oriented service

按照电话系统建模的

![20230513144432](http://image.zuoright.com/20230513144432.png)

### 无连接服务

conncetionless service

按照邮政系统建模的，报文=信件
