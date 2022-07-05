# ZeroRPC

[官网](https://www.zerorpc.io/){ .md-button .md-button--primary }

Python3 RPC方式

- xmlrpc：系统内置库`SimpleXMLRPCServer` + `xmlrpclib`，基于HTTP
- jsonrpc：第三方库`jsonrpclib` 或 `python-jsonrpc`，基于HTTP
- zerorpc：基于ZeroMQ+MessagePack的第三方库，基于TCP，由dotcloud公司在PyCon 2012大会上开源

## 基础用法

安装：`pip install zerorpc`

- 服务端

```python
import zerorpc

class Caculate(object):
    def hello(self, name):
        return 'hi, {}'.format(name)

    def add(self, x, y):
        return x + y

    def multiply(self, x, y):
        return x * y

    def subtract(self, x, y):
        return abs(x - y)

    def divide(self, x, y):
        return x / y


s = zerorpc.Server(caculate())
s.bind("tcp://0.0.0.0:4242")
s.run()
```

- 客户端

```python
import zerorpc

c = zerorpc.Client()
url = "tcp://127.0.0.1:4242"
url2 = "tcp://127.0.0.1:4343"
c.connect(url)

print(c.hello("chonge"))
print(c.add(1, 2))
```

```bash
# 还可以命令行直接调用方法
zerorpc tcp://127.0.0.1:4242 hello RPC

# 通过命令行查看远程提供的所有方法，可以grep过滤
zerorpc tcp://127.0.0.1:4242
'''
connecting to "tcp://127.0.0.1:4242"
[Caculate]
add      <undocumented>
divide   <undocumented>
hello    <undocumented>
multiply <undocumented>
subtract <undocumented>
'''

# 查看方法的文档
zerorpc --inspect tcp://127.0.0.1:4242 hello
```

## 实现细节

ZeroRPC分为三层：

- Wire (or transport) layer 利用ZeroMQ和MessagePack进行通信
- Event (or message) layer 最复杂的一层，处理心跳，事件等
- RPC layer RPC Request+Response

### ZeroMQ

是一种基于消息队列的多线程网络库，介于应用层和传输层之间，其对套接字类型、连接处理、帧、甚至路由的底层细节进行抽象，提供跨越多种传输协议的套接字。

ZMQ提供了完全不同于传统BSD Socket的网络通信接口，屏蔽了Socket的处理细节，并提供了几种通信模型，基本覆盖了分布式系统的需求。

由于ZMQ隐藏了Socket细节，所以无法感知断线，于是ZeroRPC采用心跳包的方式进行在线检测。Client端在断线后会抛出LostRemote异常。默认断线超时为30s，可设定。

### MessagePack

ZeroRPC选择MessagePack作为序列化工具，相比于JSON/BSON/YAML等格式，MsgPack提供20~50倍的序列化速度以及1/2大小的序列化产出。

## 参考

- <https://gist.github.com/ninehills/5804894>
- <https://iswbm.com/136.html>
