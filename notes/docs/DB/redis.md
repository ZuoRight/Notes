# Redis

[官方命令参考手册](https://redis.io/commands/#string){ .md-button .md-button--primary }（[中文版](http://redisdoc.com/index.html)）

Redis(Remote Dictionary Server)是一个字典结构的存储服务器，每个字典相当于一个库，从0开始递增数字命名，默认支持16个（可以通过配置文件支持更多，无上限）。

```shell
redis-server  # 启动服务端
redis-cli [-h 127.0.0.1] [-p 6379] [-a "password"]  # 连接客户端，加--raw避免中文乱码

redis 127.0.0.1:6379> ping  # 检测服务是否启动，成功返回PONG
redis 127.0.0.1:6379> info  # 查看服务端信息
redis 127.0.0.1:6379> quit  # 退出
redis 127.0.0.1:6379> select 1  # 切换到1号数据库（连接后会自动选择0号数据库）
redis 127.0.0.1:6379> CONFIG GET dir  # 获取安装目录，"/usr/local/redis/bin"
redis 127.0.0.1:6379> save  # 备份当前数据库到安装目录中的dump.rdb文件
```

## 数据模型和操作

对于键值数据库而言，基本的数据模型是 key-value 模型，在对键值数据库进行选型时，一个重要的考虑因素是它支持的 value 类型，也就是数据的保存形式：

- Memcached：仅支持String类型
- Redis：String（字符串）、List（列表）、Hash（哈希）、Set（集合）和 Sorted Set（有序集合）

Redis 能够在实际业务场景中得到广泛的应用，就是得益于支持多样化类型的 value。

![20211109191640](http://image.zuoright.com/20211109191640.png)

PUT/GET/DELETE/SCAN 是一个键值数据库的基本操作集合

## 存储方式

键值对保存在内存还是外存？保存在内存的好处是读写很快，毕竟内存的访问速度一般都在百 ns 级别。但是，潜在的风险是一旦掉电，所有的数据都会丢失。保存在外存，虽然可以避免数据丢失，但是受限于磁盘的慢速读写（通常在几 ms 级别），键值数据库的整体性能会被拉低。

Memcached 和 Redis 都是属于内存键值数据库，Redis有两种持久化存储方式

- RDB（快照）：是将数据写入一个临时文件，持久化结束后，用这个临时文件替换上次的文件，达到数据恢复。（默认）
- AOF（日志）：是将执行过的指令记录下来，数据恢复时按照从前到后的顺序再将指令执行一遍实现数据恢复。

## 访问模式

RocksDB 以动态链接库的形式使用，而 Memcached 和 Redis 则是通过网络框架访问

不同键值数据库采用的索引并不相同，不同的索引结构在性能、空间消耗、并发控制等方面具有不同的特征。

> 常见的索引的类型有：哈希表、B+ 树、字典树等。

Memcached 和 Redis 采用哈希表作为 key-value 索引，而 RocksDB 则采用的跳表。

## 操作键

命令格式：`COMMAND KEY_NAME`

```redis

```

## Python Redis

`pip install redis`

```python
import redis

r = redis.Redis(host='localhost', port=6379, decode_responses=True)

# 匹配key
r.keys()
r.keys(pattern="*abc")  # 模糊匹配

# 判断key是否存在
r.exists(key)

# 获取值
r.get('hello')
r.type('hello')
r.hgetall("test")
```
