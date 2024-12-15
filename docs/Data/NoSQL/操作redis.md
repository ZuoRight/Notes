# 操作Redis

Redis 官方推荐的两个 Python 的 Redis Client

- redis-py
- walrus，对 redis-py 的封装，目的是让使用更容易

## redis-py

`pip install redis`

```python
import redis

"""
redis.StrictRedis  标准的Redis语法
redis.Redis  是redis.StrictRedis的子类，对redis接口实现做了部分修改来兼容旧版本
"""

# 直接连接
r = redis.Redis(host='localhost', port=6379, decode_responses=True)
"""
因为每次连接和释放都需要消耗非常多的资源，所以在高并发的情况下，直接连接会耗费掉很多资源
使用连接池机制预先创建好多个连接，每次操作完放回连接池而不是直接释放掉，可以避免频繁创建和释放连接，提升整体的性能
pool = redis.ConnectionPool(host='localhost', port=6379)
r = redis.Redis(connection_pool=pool)
或者
r = redis.StrictRedis(connection_pool=pool)
"""

# 匹配key
r.keys()
r.keys(pattern="*abc")  # 模糊匹配

# 判断key是否存在
r.exists(key)

# 获取值
r.get('hello')
r.type('hello')
r.hgetall("test")
r.smembers("set_string")  # 获取集合set的成员 {'a', 'b', 'c'}

# 1千次写
for i in range(1000):
    data = {'username': 'zhangfei', 'age':28}
    r.hmset("users"+str(i), data)

# 1千次读
for i in range(1000):
    result = r.hmget("users"+str(i), ['username', 'age'])
```

## walrus

> <https://walrus.readthedocs.io/en/latest/index.html>

`pip install walrus`

```python
from walrus import *

db = Database(host='localhost', port=6379, db=0)
"""
Database是对redis.Redis类的包装，继承了其所有方法，另外还可以写成Walrus
db = Walrus(host='localhost', port=6379, db=0)
"""
```
