# Pickle

Python特有，不通用且不可阅读

- 写

```python
import pickle

# 存到内存
y = pickle.dumps(x)


# 存储到文件，必须是“wb”模式
with open("test.pickle", "wb") as f:
    pickle.dump(x, f)
```

- 读

```python
import pickle

# 从内存中取出
pickle.loads(y)


# 从文件中取出，必须是“rb”模式
with open("test.pickle", "rb") as f:
    pickle.load(f)
```
