# 操作Redis

`pip install redis`

```python
import redis

"""
redis.StrictRedis  标准的Redis语法
redis.Redis  用于向后兼容旧版本，对redis接口实现做了部分修改
"""
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
