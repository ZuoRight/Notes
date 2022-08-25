# JSON

[关于Json](https://www.json.org/json-zh.html)

JSON(JavaScript Object Notation)是一种轻量级的数据交换格式，其数据类型和JS几乎一致，实际就是JS的一个子集

在JSON出现之前，数据交换一直使用XML，虽然它本身并不复杂，但它有一大堆规范，让人头大。

但JSON出现后，很快发展为ECMA标准的数据交换格式，所有的编程语言都有解析JSON的库。

JSON规定了其键以及字符串都必须用双引号括起来，字符集必须是UTF-8。

- 对象

```json
{
  "dict1": {"k1": "v1", "k2": "v2", "k3": "v3"},
  "dict2": {
    "ka": "va",
    "kb": "vb",
    "kc": "vc"
  },
  "dict3": ["v1", "v2", "v3"]
}
```

- 数组

```json
[
  "a",
  "b",
  {
    "ka1": "va1",
    "ka2": "va2"
  },
  {
    "kb1": "vb1",
    "kb2": "vb2"
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

## 读写JSON

> 文档：<https://docs.python.org/3/library/json.html>

- 读

```python
import json

# 从内存中读取
_str = "{'a':1, 'b':2}"
json.loads(_str)
"""
需要注意的是，这里字符串类型的dict，key如果为单引号，loads时会报错，需要转换兼容一下
方式1：new_str = str.replace('\'', '\"')
方式2：new_str = json.dumps(ast.literal_eval(str))  # 需要 import ast
"""

# 从文件中读取
with open(file_path, "r", encoding='UTF-8') as f:
    result = json.load(f)
```

- 写

```python
import json

# 存到内存
cache = json.dumps(x)

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

## JSONPath

各种格式的数据反序列化(load)为Python字典(dict)后，如果想更方便的获取指定字段数据，建议使用JsonPath，相当于Xpath之于XML。

> 项目地址：<https://github.com/json-path/JsonPath>
>
> 在线解析：<https://jsonpath.com/>

`pip install jsonpath`

- `$` 根节点
- `@` 当前节点
- `.` 或者 `[]` 子节点
- `..` 模糊匹配
- `*` 匹配所有节点
- `[]` 迭代器标示，可用于数组下标[0]，范围选择[a,c]
- `()` 支持表达式计算
- `?()` 过滤操作

```json
{
    "A": 123,
    "B": "str",
    "C":
    {
        "C1":[1,2,3],
        "C2":
        {
            "C2_1":
            [
                {"id":1, "name":"yi"},
                {"id":2, "name":"er"},
                {"id":3, "name":"san"}
            ]
        }
    }
}
```

```python
import jsonpath

# 结果以list形式返回，匹配不到结果则返回False
jsonpath.jsonpath(json_obj,'$..name')  # 获取所有“name”的值，['yi', 'er', 'san']
jsonpath.jsonpath(json_obj,'$..C2_1[*].id')  # 获取所有id的值，[1, 2, 3]
jsonpath.jsonpath(json_obj,'$..C2_1[1]')  # 获取C2_1对应列表的第二个数据，[{'id': 2, 'name': 'er'}]
jsonpath.jsonpath(json_obj,'$..C2_1[(@.length-1)]')  # 获取C2_1对应列表的倒数第一个，[{'id': 3, 'name': 'san'}]
jsonpath.jsonpath(json_obj,'$..C2_1[?(@.id<2)]')  # 过滤出C2_1对应列表中id小于2的值，[{'id': 1, 'name': 'yi'}]
```

另一个解析JSON的库：[jmespath](https://jmespath.org/tutorial.html)，待研究...
