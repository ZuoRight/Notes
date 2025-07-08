# Zookeeper

Zookeeper 是一个分布式协调中间件服务，不仅可以用于服务注册和发现，还可用于分布式锁、配置管理等场景

从官网下载二进制包：<https://zookeeper.apache.org/releases.html>

```shell
tar -xzf apache-zookeeper-3.8.0-bin.tar.gz
cd apache-zookeeper-3.8.0-bin
cp conf/zoo_sample.cfg conf/zoo.cfg  # 从模版创建配置文件
bin/zkServer.sh start  # 启动 zookeeper 服务
```

Python 客户端

`pip install kazoo`

```python
from kazoo.client import KazooClient

# 连接到本地 ZooKeeper
zk = KazooClient(hosts='127.0.0.1:2181')
zk.start()

# 创建节点
zk.create("/my_node", b"hello")

# 读取节点
data, stat = zk.get("/my_node")
print(f"数据: {data.decode()}")  # 输出: hello

# 监听节点变化
def watch_node(data, stat):
    print(f"节点更新: {data.decode()}")

zk.get("/my_node", watch=watch_node)

# 更新节点
zk.set("/my_node", b"world")

# 删除节点
zk.delete("/my_node")

zk.stop()  # 关闭连接
```
