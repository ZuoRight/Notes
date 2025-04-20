# Dubbo

<https://cn.dubbo.apache.org/zh-cn/overview/what/>

Apache Dubbo 是一款 RPC 服务开发框架，用于解决微服务架构下的服务治理与通信问题。

但它不仅仅是一款 RPC 框架。首先，可以使用多种通信协议，其次，提供了丰富的服务治理能力与生态。

支持多种协议

协议|描述|适用场景|默认端口
:---|:---|:---|:---
tri	    |基于 HTTP/2 的 Triple 协议（Dubbo 3 默认）	    |多语言互通、云原生、gRPC 兼容场景  |50051
dubbo	|Dubbo 2 的私有协议（基于 TCP + Hessian2 序列化）|高性能 Java 内部服务调用          |20880
http	|基于 HTTP/1.1 的 RESTful 协议（如 JSON-RPC）	|需要通用 HTTP 调用的场景          |8080
grpc	|标准 gRPC 协议（Dubbo 3 兼容）	                |需要与原生 gRPC 服务互操作
rest	|REST over HTTP（如 JSON/XML）	              |开放给前端或移动端的 API
thrift	|Apache Thrift 协议	                          |需要与 Thrift 生态交互

自 Dubbo3 开始，默认使用基于 HTTP/2 的 Triple 协议，提供了除 Java 外的多语言 SDK 实现，比如 [Python SDK](https://cn.dubbo.apache.org/zh-cn/overview/mannual/python-sdk/quick-start/)

由于 dubbo 协议默认使用 Hessian2 序列化，Python 中缺乏完善支持，索性移除了序列化层，支持自定义实现：<https://cn.dubbo.apache.org/zh-cn/overview/mannual/python-sdk/custom-serialization/>，比如使用 [protobuf](https://protobuf.dev/getting-started/pythontutorial/) 或 json。

```shell
git clone https://github.com/apache/dubbo-python.git
cd dubbo-python
pip install .  # 安装 pyproject.toml 中 apache-dubbo 相关依赖

# 启动服务端 & 客户端
python samples/helloworld/server.py
python samples/helloworld/client.py
```

- 搭配 zookeeper 使用

```shell
# 启动 zookeeper 服务
cd apache-zookeeper-3.8.0-bin
bin/zkServer.sh start

# dubbo 环境中安装 zookeeper 相关依赖，即 kazoo
pip install .[zookeeper]
pip install protobuf
# 启动 dubbo 服务端 & 客户端
python samples/registry/zookeeper/server.py
python samples/registry/zookeeper/client.py
```
