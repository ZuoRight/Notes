# Tablib

[官方文档](https://tablib.readthedocs.io/en/stable){ .md-button .md-button--primary }

```shell
pip install tablib  # 安装，更新加参数：--upgrade
pip install "tablib[xlsx]"  # 安装某个依赖
pip install "tablib[html, pandas, ods, xls, xlsx, yaml]"  # 安装多个依赖
pip install "tablib[all]"  # 安装所有依赖
```

```python
import tablib

headers = ('first_name', 'last_name', 'age')
data = [
    ('John', 'Adams', '90'),
    ('Henry', 'Ford', '83')
]

data = tablib.Dataset(*data, headers=headers)

>>> data.dict  # 查看目前表中的所有数据
[OrderedDict([('First Name', 'John'), ('Last Name', 'Adams')]), OrderedDict([('First Name', 'George'), ('Last Name', 'Washington')]), OrderedDict([('First Name', 'Henry'), ('Last Name', 'Ford')])]

>>> print(data)  # 会打印格式化的数据，非常nice
First Name|Last Name|age
----------|---------|---
John      |Adams    |90
Henry     |Ford     |83

>>> data.height  # 输出当前记录(行)总数
2
>>> data.width  # 输出当前属性(列)总数
3
```

## json转换为xlsx

```python
def json2xlsx(json_file, xlsx_file):
    data = []
    with open(json_file, 'r', encoding='UTF-8', errors='ignore') as f:
        rows = json.load(f)  # [{"a":1},{"a":2},{"a":3}]
        # 将第一行数据的key作为表头, 也可以自定义
        header = tuple([ i for i in rows[0].keys()])
        for row in rows:
            body = []
            # 将每行的value取出组成值列表
            for v in row.values():
                body.append(v)
            # 将所有行的值元组/列表组合到一起
            data.append(tuple(body))
    # 固定方法，将含标题和内容的数据放到data里
    data = tablib.Dataset(*data, headers=header)
    # 数据写入xlsx文件
    open(xlsx_file, 'wb').write(data.xlsx)

json2xlsx('demo.json', 'demo.xlsx')
```
