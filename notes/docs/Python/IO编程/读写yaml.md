# YAML

## PyYAML

`pip install pyyaml`

```python
import yaml

# 读取
def read_from_yaml(path):
    with open(path, "r", encoding="utf-8") as f:
        return yaml.safe_load(f.read())

# 写入
def write_to_yaml(content, path):
    with open(path, "w", encoding="utf-8") as f:
        # allow_unicode=True 保证中文写入正常显示
        yaml.dump(content, f, default_flow_style=False, encoding='utf-8', allow_unicode=True)

# 修改
data = read_from_yaml(path)
data["key"] = value
write_to_yaml(data, path)
```

## 基础语法

- 注释符：#
- 大小写敏感
- 缩进（必须空格，数量不限）表示层级（同级必须对齐）

### list

`[v1, v2, v3]`

```yaml
- v1
- v2
- v3
```

`[[1, 2], [3, 4]]`

```yaml
-
 - 1
 - 2
-
 - 3
 - 4
```

### dict

`{'k1': 'v1', 'k2': 'v2'}`

```yaml
k1: v1
k2: v2
```

```yaml
- k1:v1
- k2:v2
- k3:v3
```

`{'k1': [1, 2], 'k2': {'k33': 'v33', 'k44': 'v44'}}`

```yaml
k1:
  - 1
  - 2
k2:
  k33: v33
  k44: v44
```

### 纯量


`[True, False, 100, 1.23, None, "字符串"， "I am 7c"]`

```yaml
- true  # True
- false  # False
- 100  # int
- 1.23  # float
- ~  # None
- 字符串  # 无需双引号
- "I am 7c"  # 带空格或特殊字符的字符串，必须用双引号
```
