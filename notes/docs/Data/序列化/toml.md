# TOML

> 语法参考：<https://toml.io/cn/>

- 数组/列表

```toml
list1 = ["v1", "v2", "v3"]
list2 = ["va", "vb", "vc"]
```

- 对象

```toml
[dict1]
k1 = "v1"
k2 = "v2"
k3 = "v3"
dict2 = {ka="va", kb="vb", kc="vc"}
```

- 嵌套

```toml
[[list_dict]]
ka1 = "va1"
ka2 = "va2"
ka3 = "va3"

[[list_dict]]
kb1 = "vb1"
kb2 = "vb2"
kb3 = "vb3"

[[list_dict]]
kc1 = "vc1"
kc2 = "vc2"
kc3 = "vc3"
```

- 纯量

```toml
# 字符串
str = "hello world"

int = 77
float = 3.14
exponential = 2.0e+3  # 2x10^3
bool1 = [true, false]

date = 1979-05-27
```

## 读写TOML

`pip install toml`

```python
import toml

# 序列化并写入
with open(file_path, "w", encoding='UTF-8') as f:
    toml.dump(content, f)

# 读取并反序列化
with open(file_path, "r", encoding='UTF-8') as f:
    content = toml.load(file_path)
```
