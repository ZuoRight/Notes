# Redis

[官方文档](https://redis.io/docs/getting-started/){ .md-button .md-button--primary }

- [命令查询](https://redis.io/commands/#string)
- [在线演练](https://try.redis.io/)

Redis(Remote Dictionary Server)是一个字典结构的基于内存的数据库，可以归为缓存。

Redis 使用 ANSI C 语言编写，采用单进程单线程模型和非阻塞多路 I/O 复用机制，查询效率非常高，根据官方提供的数据，每秒最多处理的请求可以达到 10 万次。

MySQL 作为数据库，提供持久化功能，并通过主从架构提高数据库服务的高可用性，实际场景中大多数情况下可能都是读操作，可以用 Redis 作为 MySQL 的缓存，降低 I/O 的访问，解决读写效率要求很高的请求，不过它存储的数据量有限，适合存储热点数据。

![20240809170218](https://image.zuoright.com/20240809170218.png)

## redis-server

- 源码安装

```shell
wget https://download.redis.io/redis-stable.tar.gz
tar -xzvf redis-stable.tar.gz
cd redis-stable
make
make install  # 安装到：/usr/local/bin
```

- Ubuntu

```shell
curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list

sudo apt-get update
sudo apt-get install redis
```

- Windows 官方不支持
- Mac

```shell
brew install redis  # 安装

# 前台启动服务，Ctrl-C停止服务
redis-server
# 后台启动服务
brew services start/stop redis
```

- Docker

> <https://hub.docker.com/_/redis>

```shell
# 启动服务
docker run --name some-redis -d redis
"""
docker run --name some-redis -d redis redis-server --save 60 1 --loglevel warning
持久化存储，可自定义路径：-v /docker/host/dir:/data
"""
# 连接
docker run -it --network some-network --rm redis redis-cli -h some-redis
```

## redis-cli

连接服务

```shell
redis-cli
'
-h 127.0.0.1
-p 6379
-a "password" 测试环境可省略
--raw  避免中文乱码
'
```

连接后，命令行提示符显示为：`127.0.0.1:6379>`

```shell
ping  # 检测服务是否启动，成功返回PONG
info  # 查看服务端信息
quit  # 退出连接

CONFIG GET dir  # 获取安装目录，"/usr/local/redis/bin"
save  # 备份当前数据库到安装目录中的dump.rdb文件

"""
默认支持16个库（可以通过配置文件支持更多，无上限），从0开始递增数字命名
连接后会自动选择0号数据库，可手动切换
"""
select 1
```

## GUI 客户端

- RESP

以前叫做 Redis Desktop Manager，目前已收费，可试用14天，官网个人版 ¥129/年，苹果/微软应用商店¥98/永久（不能跨平台使用）

- [Another Redis Desktop Manager](https://github.com/qishibo/AnotherRedisDesktopManager)

可能是目前最好的开源客户端

Mac 上启动如果报错可以执行：`sudo xattr -rd com.apple.quarantine /Applications/Another\ Redis\ Desktop\ Manager.app`

![20240416112136](https://image.zuoright.com/20240416112136.png)

## 数据模型和操作

对于键值数据库而言，基本的数据模型是 key-value 模型，在对键值数据库进行选型时，一个重要的考虑因素是它支持的 value 类型，也就是数据的保存形式

- Memcached 仅支持 String 类型
- Redis 能够在实际业务场景中得到广泛的应用，就是得益于支持多样化类型的 value。

不同键值数据库采用的索引并不相同，不同的索引结构在性能、空间消耗、并发控制等方面具有不同的特征

> 常见的索引的类型有：哈希表、B+ 树、字典树等。

- RocksDB 采用跳表
- Memcached 和 Redis 则采用哈希表作为 key-value 索引。

![20211109191640](http://image.zuoright.com/20211109191640.png)

```shell
type key  # 查看key的类型
del key  # 删除key
```

### String

字符串

```shell
# set key value
set name zhangsan
# 获取
get name
```

### Hash

字符串类型的哈希，类似Python的Dict

```shell
# 添加：hset key field value
hset dict1 username zhangsan
hset dict1 age 28

# 获取field
hkeys dict
"""
1) "username"
2) "age"
"""

# 获取value
hget user1 age  # "28"

# 删除字段
hdel dict1 age

# 设置多个field：hmset key field1 value1 field2 value2...
hmset dict1 username zhangsan age 28

# 获取多个field的value
hmget user1 username age
"""
1) "zhangsan"
2) "28"
"""
```

### List

有序的字符串列表，按插入顺序排序

```shell
# LPUSH/RPUSH key value [...]
lpush list zhangfei guanyu liubei  # 向列表左侧添加元素
rpush list dianwei lvbu  # 向列表右侧添加元素

# 删除
lrem list num value
"""
num=0 删除所有value元素
num>0 从左边查找，删除第n个
num<0 从右边查找，删除第n个
"""

# 获取
llen list  # 返回列表长度
lindex list 1  # 获取指定index的元素
# LRANGE key start stop
lrange list 0 2
"""
1) "liubei"
2) "guanyu"
3) "zhangfei"
"""
```

### Set

无序的字符串集合，元素不能重复，与Python集合类似

```shell
# SADD key member [...]
sadd sets zhangfei guanyu liubei dianwei lvbu

# 删除
srem sets liubei lvbu
spop sets  # 随机删除并返回被删除元素

# 获取
scard set  # 获取集合长度
smembers sets  # 获取集合元素
```

### SortedSet

有序的字符串集合，元素不能重复，简称ZSet，按score排序（添加元素时指定score）

ZSet与List都是有序的，区别在于底层实现不同，List操作两端的元素很快，但操作中间元素较慢，而ZSet操作中间元素也很快，而且通过score调整顺序也比List更高效，只需要改变score大小即可

```shell
# ZADD key score member [...]
zadd heroScore 8341 zhangfei 7107 guanyu 6900 liubei 7516 dianwei 7344 lvbu

# 删除
zrem heroScore guanyu

# 获取
zcard heroScore  # 获取集合长度
zscore heroScore liubei  # 获取score

# 排序
ZREVRANGE heroScore 0 2  # 显示排名前三的英雄及数值
"""
ZREVRANGE key start stop [WITHSCORES]  # 按score从大到小排
ZRANGE key start stop [WITHSCORES]  # 按score从小到大排
可选项withscore 显示score分数
"""
```

### Streams

数据流，5.0版本新增的，主要用于消息队列

## 过期数据处理

随着缓存的数据越来越多，需要给缓存加一个过期时间（由应用程序设置），定期清理过期数据，但如果全部扫描一遍要花费大量时间，所以选择随机删除，但有可能某些数据一直不会被随机算法选中，所以又增加了惰性删除，即如果有请求访问到过期数据，则立刻删除，但如果有些键值一直没有请求，则依然不会被删除而占据内存空间，所以还需要内存淘汰策略。

查询的数据如果不存在，则无法缓存，称之为缓存穿透

如果某热点数据缓存过期被删除时恰好遇到大规模请求，则请求会抵达mysql，称之为缓存击穿。

如果恰巧一大批热点数据缓存同时过期，则会发生缓存雪崩。

所以应用程序最好将缓存过期时间设置的分散一些，还可以将热点数据设置为永不过期。

## 持久化存储

Memcached 和 Redis 都是属于内存键值数据库，但Redis有两种持久化存储方式

> 键值对保存在内存和外存的优缺点  
> 保存在内存的好处是读写很快，毕竟内存的访问速度一般都在百 ns 级别，但是，潜在的风险是一旦掉电，所有的数据都会丢失。  
> 保存在外存，虽然可以避免数据丢失，但是受限于磁盘的慢速读写（通常在几 ms 级别），键值数据库的整体性能会被拉低。

- RDB（快照，默认）：是将数据写入一个临时文件，持久化结束后，用这个临时文件替换上次的文件，达到数据恢复。
- AOF（日志）：是将执行过的指令记录下来，数据恢复时按照从前到后的顺序再将指令执行一遍实现数据恢复。

## 问题画像图

![20240809170025](https://image.zuoright.com/20240809170025.png)
