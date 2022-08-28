# YAML

YAML Ain't a Markup Language

> 参考文档：<https://www.runoob.com/w3cnote/yaml-intro.html>

- 对象

```yaml
ka: {k1: v1, k2: v2, k3: v3}  # key不需要引号，value都可，建议用
kb:
  k1: v1  # 冒号后必须带空格？
  k2: v2
  k3: v3
kc: [v1, v2, v3]
```

- 数组

```yaml
k:
  - va
  - vb
  - {k1: "v1", k2: "v2", k3: "v3"}
```

- 纯量

```yaml
# 字符串，带空格或特殊字符的字符串，建议用双引号
str: hello world
# 多行，每句末尾自动加换行（如果想加两个用：|+，不想加用：|-）
multiline: |
  one line
  two line
  three line

integer: 77
float: 3.14
octal_number: 010  # 八进制数：8
hexadecimal: 0xF  # 十六进制数：15
exponential: 2.0e+3  # 2x10^3=2000

bool: [true, false, ~]

# 时间使用ISO-8601格式
date_simple: 2021-08-14
# 时间和日期之间使用T连接，最后使用+代表时区
date_iso: 2021-08-14T15:02:31+08:00
```

## 读写YAML

`pip install pyyaml`

- 读

```python
import yaml

def read_from_yaml(file_path):
    with open(file_path, "r", encoding="utf_8") as f:
        return yaml.safe_load(f.read())
```

- 写

```python
import yaml

def write_to_yaml(content, file_path):
    with open(file_path, "w", encoding="utf_8") as f:
        # allow_unicode=True 保证中文写入正常显示
        yaml.dump(content, f, default_flow_style=False, encoding='utf-8', allow_unicode=True)
```

- 修改

```python
data = read_from_yaml(file_path)
data["key"] = value
write_to_yaml(data, file_path)
```
