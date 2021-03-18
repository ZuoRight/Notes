# Redis

```python
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
