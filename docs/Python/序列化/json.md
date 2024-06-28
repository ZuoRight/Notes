# JSON

[关于 JSON](https://www.json.org/json-zh.html)

JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式，其数据类型和JS几乎一致，实际就是JS的一个子集

在 JSON 出现之前，数据交换一直使用 XML，虽然它本身并不复杂，但它有一大堆规范，让人头大。

但 JSON 出现后，很快发展为 ECMA 标准的数据交换格式，所有的编程语言都有解析JSON的库。

JSON 规定了其键以及字符串都必须用双引号括起来，字符集必须是 UTF-8。

- 对象

```json
{
  "ka": {"k1": "v1", "k2": "v2", "k3": "v3"},
  "kb": {
    "k1": "v1",
    "k2": "v2",
    "k3": "v3"
  },
  "kc": ["v1", "v2", "v3"]
}
```

- 数组

```json
[
  "va",
  "vb",
  {
    "k1": "v1",
    "k2": "v2"
  }
]
```

- 纯量

```json
{
  "str": "hello",
  "integer": 77,
  "octal_number": 8,
  "hexadecimal": 15,
  "float": 3.14,
  "exponential": 1.2e+32,
  "bool": [true, false, null],
  "date_simple": "2021-08-14",
  "date_iso": "2016-08-14 13:46:07 -0500"
}
```

## JSON 操作

> 文档：<https://docs.python.org/3/library/json.html>

内置的 `json` 库在处理大数据时，性能较差，可以考虑使用基于 C 语言的 `ujson` 或基于 Rust 的 `ojson`

- 序列化

```python
import json

# 序列化为字符串对象
x = 1
_str = json.dumps(x)  # "1"

# 序列化并存储到json文件
with open(file_path, "w", encoding='UTF-8') as f:
    json.dump(x, f, indent=4, ensure_ascii=False, sort_keys=True, separators=(',', ':'))
    """
    :indent  设置缩进显示成几个空格
    :ensure_ascii  是否显示ascii码，默认为true，如果要显示中文需要设置为false
    :sort_keys  是否按照字母排序
    :separators  设置分隔符
    """
```

- 反序列化

```python
import json

# 从字符串对象读取并反序列化为Python对象
_str = '{"a":1, "b":2}'
json.loads(_str)
"""
需要注意的是，这里字符串类型的dict，key如果为单引号，loads时会报错，需要转换兼容一下
方式1：new_str = str.replace('\'', '\"')
方式2：new_str = json.dumps(ast.literal_eval(str))  # 需要 import ast
"""

# 从文件对象读取并反序列化为dict
with open(file_path, "r", encoding='UTF-8') as f:
    result = json.load(f)
```
