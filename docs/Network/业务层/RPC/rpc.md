# RPC

RPC（Remote Procedure Call）远程服务调用，RPC 本身并不是一个具体的协议，而是一种调用方式，即屏蔽掉一些细节，像调用本地方法一样调用远程方法

RPC 只是定义了一个通信接口，其底层的实现可以各不相同，可以是 Socket，也可以是消息队列等。

![20220404180919](https://image.zuoright.com/20220404180919.png)

## 发展过程

CORBA 是一套由国际标准组织牵头，由多家软件提供商共同参与的分布式规范，支持跨系统、跨语言的 CORBA 原本是最有机会统一 RPC 这个领域的有力竞争者，但它设计的过于繁琐，制订的规范晦涩难懂，脱离实际，各门语言有各自的解读，最终的实现互不兼容，简直荒谬。

然后又出现了 Web Service，采用了 XML（当时被认为是计算机工业的最新银弹）和 SOAP，性能差是一方面，另一方面是它希望在一套协议上一揽子解决分布式计算中可能遇到的所有问题，这促使 Web Service 生出了一整个家族的协议出来，对开发者造成了非常沉重的学习负担，最终被放弃。

由于一直没有一个能同时满足简单、普适、高性能的 RPC 协议出现，所以远程服务调用这个小小的领域便陷入了群雄混战百家争鸣的局面。

- 朝着面向对象发展的：CORBA、RMI（Sun/Oracle）等
- 朝着简化发展的：JSON-RPC 2.0（JSON-RPC工作组），功能最弱，速度最慢
- 朝着高性能发展的：Thrift（Facebook/Apache）、gRPC（Google）等
- 朝着插件化发展的：Thrift、Dubbo（阿里/Apache）

> Dubbo 默认采用 Hessian 2 作为序列化器，可以根据需求替换为其它的，默认有自己的传输协议（Dubbo协议），同时也支持其它协议

## 实现原则

RPC 是构建分布式系统的基石，当一个应用系统从单体演进为分布式时，会被拆分为多个不同功能的应用，不同应用可以使用不同的语言，也可以分别部署在不同的服务器上，如何像调用本地服务一样调用远程服务，传输层一般还是基于 TCP/UDP 等协议来完成，然后需要考虑以下三个问题

- 表示数据

即序列化与反序列化

比如 CORBA 的 CDR，Web Service 的 XML，gRPC 的 Protocol，以及通用的 JSON 等等

- 交换数据

要考虑异常、超时、安全、认证、授权、事务等等可能产生双方需要交换信息的需求，即：Wire Protocol。

比如 CORBA 的 IIOP，Web Service 的 SOAP，JSON-RPC 的 HTTP 等等。

- 确定方法

即跨语言如何找到对应的方法，最粗暴的方式就是给每个方法都规定一个不重复的编号（UUID）但终究还是需要一套语言无关的接口描述语言（Interface Description Language，IDL），类似的协议有 CORBA 的 OMG IDL，Web Service 的 WSDL，JSON-RPC 的 JSON-WSP 等等。

## 应用

RPC 不仅可以用来解决通信问题，还被用在了很多其他场景，比如：发 MQ、分布式缓存、数据库等，它就像分布式系统的经脉一样，通常是被封装在各个框架中使用，以至于我们忘记 RPC 的存在。

![20220404181703](https://image.zuoright.com/20220404181703.png)

进程内调用方便是因为写操作系统的人把复杂的事情给处理了，让业务研发专注于业务逻辑。进程间调用简单是因为写 RPC 框架的人把复杂的工作给做了，使业务研发继续专注于业务逻辑。

进程间能通信是第一步，后面随着机器的增多事情就变得复杂了起来，所以，有了服务注册中心、有了服务治理平台、有了配置管理中心、有了容器平台、有了各个各有的监控平台、有了 UMP/UCC/LOGBOOK/MDC/OPS/QONE/JONE/CAP 等等一系列的东西。不过问题的根源还是单机的性能及容量有上限导致的，否则也就没这么多的事情了。

## 参考

- <https://icyfenix.cn/architect-perspective/general-architecture/api-style/rpc.html>
- <https://time.geekbang.org/column/intro/100046201?tab=intro>
