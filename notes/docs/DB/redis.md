# Redis

[官方文档](https://redis.io/docs/getting-started/){ .md-button .md-button--primary }

- [命令查询](https://redis.io/commands/#string)
- [在线演练](https://try.redis.io/)

Redis(Remote Dictionary Server)是一个字典结构的基于内存的数据库，可以归为缓存。

Redis 使用 ANSI C语言编写，采用单进程单线程模型和非阻塞多路I/O复用机制，查询效率非常高，根据官方提供的数据，每秒最多处理的请求可以达到10万次。

> 每个字典相当于一个库，从0开始递增数字命名，默认支持16个（可以通过配置文件支持更多，无上限）

Redis 通常可以作为 MySQL 的缓存，它存储的数据量有限，适合存储热点数据，可以解决读写效率要求很高的请求。而 MySQL 则作为数据库，提供持久化功能，并通过主从架构提高数据库服务的高可用性。

## redis-cli

```bash
redis-server  # 启动服务端
redis-cli [-h 127.0.0.1] [-p 6379] [-a "password"]  # 连接客户端，加--raw避免中文乱码
```

连接后，命令行提示符显示为：`redis 127.0.0.1:6379>`

```bash
ping  # 检测服务是否启动，成功返回PONG
info  # 查看服务端信息
quit  # 退出

CONFIG GET dir  # 获取安装目录，"/usr/local/redis/bin"
save  # 备份当前数据库到安装目录中的dump.rdb文件

select 1  # 切换到1号数据库（连接后会自动选择0号数据库）
```

## 数据模型和操作

对于键值数据库而言，基本的数据模型是 key-value 模型，在对键值数据库进行选型时，一个重要的考虑因素是它支持的 value 类型，也就是数据的保存形式：

- Memcached：仅支持String类型
- Redis：String（字符串）、List（字符串列表）、Hash（哈希）、Set（字符串无序集合）和 SortedSet（字符串有序集合，简称ZSet）

Redis 能够在实际业务场景中得到广泛的应用，就是得益于支持多样化类型的 value。

> 除了5种基础数据类型以外，Redis 还支持位图（Bitmaps）数据结构，在 2.8 版本之后，增加了基数统计（HyperLogLog），3.2 版本之后加入了地理空间（Geospatial）以及索引半径查询的功能，在 5.0 版本引用了数据流（Streams）数据类型。

![20211109191640](http://image.zuoright.com/20211109191640.png)

- String

```bash
# set key value
set name zhangsan
# 获取
get name
```

- Hash

```bash
# hset key field value
hset user1 username zhangsan
hset user1 age 28
# hmset key field value [field value...]
hmset user1 username zhangsan age 28
# 获取
hget user1 username
hmget user1 username age
```

- List

```bash
# LPUSH/RPUSH key value [...]
LPUSH heroList zhangfei guanyu liubei  # 向列表左侧添加元素
RPUSH heroList dianwei lvbu  # 向列表右侧添加元素
# 获取
# LRANGE key start stop
LRANGE heroList 0 4
```

- Set

```bash
# SADD key member [...]
SADD heroSet zhangfei guanyu liubei dianwei lvbu
# 删除
SREM heroSet liubei lvbu
# 获取
SMEMBERS heroSet  # 获取所有
SISMEMBER heroSet zhangfei  # 获取某个
```

- ZSet（SortedSet）

ZSet与普通Set的区别在于有序，在给有序集合 key 添加 member 的时候，可以指定 score，ZSET 会按照分数来进行自动排序

ZSet与Listbi都是有序的，区别在于底层实现不同，List操作两端的元素很快，但操作中间元素较慢，而ZSet操作中间元素也很快，而且通过score调整顺序也比List更高效

```bash
# ZADD key score member [...]
ZADD heroScore 8341 zhangfei 7107 guanyu 6900 liubei 7516 dianwei 7344 lvbu
# 删除
ZREM heroScore guanyu
# 获取
ZSCORE heroScore liubei
# 排序
"""
ZRANGE key start stop [WITHSCORES]  # 从小到大
ZREVRANGE key start stop [WITHSCORES]  # 从大到小
可选项[WITHSCORES] 显示score
"""
ZREVRANGE heroScore 0 2  # 显示排名前三的英雄及数值
```

## 持久化存储

Memcached 和 Redis 都是属于内存键值数据库，但Redis有两种持久化存储方式

> 键值对保存在内存和外存的优缺点  
> 保存在内存的好处是读写很快，毕竟内存的访问速度一般都在百 ns 级别，但是，潜在的风险是一旦掉电，所有的数据都会丢失。  
> 保存在外存，虽然可以避免数据丢失，但是受限于磁盘的慢速读写（通常在几 ms 级别），键值数据库的整体性能会被拉低。

- RDB（快照，默认）：是将数据写入一个临时文件，持久化结束后，用这个临时文件替换上次的文件，达到数据恢复。
- AOF（日志）：是将执行过的指令记录下来，数据恢复时按照从前到后的顺序再将指令执行一遍实现数据恢复。

## 访问模式

RocksDB 以动态链接库的形式使用，而 Memcached 和 Redis 则是通过网络框架访问

不同键值数据库采用的索引并不相同，不同的索引结构在性能、空间消耗、并发控制等方面具有不同的特征。

> 常见的索引的类型有：哈希表、B+ 树、字典树等。

Memcached 和 Redis 采用哈希表作为 key-value 索引，而 RocksDB 则采用的跳表。
