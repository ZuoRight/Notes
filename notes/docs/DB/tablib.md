# Tablib

[官方文档](https://tablib.readthedocs.io/en/stable){ .md-button .md-button--primary }

Tablib用于处理表格类数据（tabular dataset），为不同格式的数据集提供一个统一的格式

```shell
pip install tablib  # 安装，更新加参数：--upgrade
pip install "tablib[xlsx]"  # 安装某个依赖
pip install "tablib[html, pandas, ods, xls, xlsx, yaml]"  # 安装多个依赖
pip install "tablib[all]"  # 安装所有依赖
```

```python
import tablib
"""
tablib.Databook() 相当于Excel中的WorkBook，即Sheet的集合
tablib.Dataset() 相当于Excel中的Sheet
"""

# 构建Dataset对象
headers = ('first_name', 'last_name', 'age')  # tuple或list都可以
body = [
    ('John', 'Adams', '90'),
    ('Henry', 'Ford', '83')
]

"""方式1"""
data = tablib.Dataset(*body, headers=headers)
"""方式2"""
data = tablib.Dataset()
data.headers = headers
data.append(['Chonge', 'Jiang', '30'])  # 添加行
data.append_col([66, 77, 88], header='score')  # 添加列

# 从文件读取
with open('tmp/demo_case.xlsx', 'rb') as f:
    """方式1"""
    # data = tablib.import_set(f.read(), 'xls', headers=False)  # 如果没有表头需要加上headers=False
    """方式2"""
    data = tablib.Dataset().load(f.read(), 'xlsx')

# 返回子Dataset
subset(rows=None, cols=None)
```

```python
data.dict  # 查看目前表中的所有数据
"""
[OrderedDict([('First Name', 'John'), ('Last Name', 'Adams')]), OrderedDict([('First Name', 'George'), ('Last Name', 'Washington')]), OrderedDict([('First Name', 'Henry'), ('Last Name', 'Ford')])]
"""

data.height  # 输出当前记录(行)总数，2
data.width  # 输出当前属性(列)总数，3
```

```python
print(data)  # 会打印格式化的数据，非常nice
"""
First Name|Last Name|age
----------|---------|---
John      |Adams    |90
Henry     |Ford     |83
"""

# 获取行
print(data.headers)  # 表头
print(data[0])  # 第1行
print(data[:2])

# 遍历除表头外的行
for i in data:
    print(i)

# 获取列
print(data["first_name"])  # 根据表头名称
print(data.get_col(0))  # 根据表头索引
```

```python
# 导出数据
with open('demo.xls', 'wb') as f:
    f.write(data.xls)
```

## 动态列

<https://xin053.github.io/2016/07/10/tablib%E5%BA%93%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3/>

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
