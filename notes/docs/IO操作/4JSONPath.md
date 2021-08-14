# JSONPath

各种格式的数据反序列化(load)为Python字典(dict)后，如果想更方便的获取指定字段数据，建议使用JsonPath，相当于Xpath之于XML。

`pip install jsonpath`

[常用语法](https://goessner.net/articles/JsonPath/)

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
