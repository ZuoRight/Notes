# Tablib

[官方文档](https://tablib.readthedocs.io/en/stable){ .md-button .md-button--primary }

Tablib用于处理表格类数据（tabular dataset），为不同格式的数据集提供一个统一的格式

```bash
pip install tablib  # 安装，更新加参数：--upgrade
pip install "tablib[xlsx]"  # 安装某个依赖
pip install "tablib[html, pandas, ods, xls, xlsx, yaml]"  # 安装多个依赖
pip install "tablib[all]"  # 安装所有依赖
```

其实就是将操作各种格式的库封装了一层，比如操作`.xlsx`，底层依赖的依然是openpyxl这个库

## 构建Dataset

- `tablib.Databook()` 相当于Excel中的WorkBook，即Sheet的集合
- `tablib.Dataset()` 相当于Excel中的Sheet

```python
import tablib

headers = ('first_name', 'last_name', 'age')  # tuple或list都可以
body = [
    ('John', 'Adams', '90'),
    ('Henry', 'Ford', '83')
]

# 方式1
data = tablib.Dataset(*body, headers=headers)

# 方式2
data = tablib.Dataset()
data.headers = headers
data.append(['Chonge', 'Jiang', '30'])  # 添加行
data.append_col([66, 77, 88], header='score')  # 添加列

# 返回子Dataset
data.subset(rows=None, cols=None)
```

## 导入数据

```python
class GetData:
    def __init__(self, path):
        data_path = data_path.lower()
        data_format = data_path.split('.')[1]
        data_format = data_path.split('.')[1]
        """
        excel文件需要用rb模式打开
        csv、json等可以用r模式打开
        """
        if data_format in ("xls", 'xlsx'):
            with open(data_path, 'rb') as fh:
                self.data = tablib.Dataset().load(fh, data_format, headers=True)
                """
                format默认为None，加载时会自动分析文件格式(excel等需要先安装对应依赖)，不过建议指定格式，否则可能会报莫名其妙的错误
                另外，如果没有表头，需要设置：headers=False
                """
        else:
            with open(data_path, 'r') as fh:
                self.data = tablib.Dataset().load(fh)


if __name__ == "__main__":
    case = GetData("data/demo.xlsx")


# 如果导入的是json，最外层格式必须是[]形式的
"""
[
    {
        "Last Name": "Reitz",
        "First Name": "Kenneth",
        "Age": 22
    },
    {
        "Last Name": "Monke",
        "First Name": "Bessie",
        "Age": 20
    }
]
"""

# yaml
"""
- {Age: 22, First Name: Kenneth, Last Name: Reitz}
- {Age: 20, First Name: Bessie, Last Name: Monke}
"""
```

## 导出数据

```python
data.export('json')
```

```python
# 导出数据
with open('demo.xls', 'wb') as f:
    f.write(data.xls)
```

## 获取数据

注意：实例化后将不能间接调用data的各种属性和方法

```python
# 打印格式化的数据
print(data)  # <class 'tablib.core.Dataset'>
"""
First Name|Last Name|age
----------|---------|---
John      |Adams    |90
Henry     |Ford     |83
"""

data.height  # 输出当前记录(行)总数，2
data.width  # 输出当前属性(列)总数，3
data.headers  # 获取表头
headers.index("col_name")  # 获取表头索引

# 查看目前表中的所有数据
data.dict
"""
[
    OrderedDict([('First Name', 'John'), ('Last Name', 'Adams')]), 
    OrderedDict([('First Name', 'George'), ('Last Name', 'Washington')]), 
    OrderedDict([('First Name', 'Henry'), ('Last Name', 'Ford')])
]
"""

# 获取行
data[0]  # 第1行
data[:n]  # 前n行

# 获取列
print(data["first_name"])  # 根据表头名称
print(data.get_col(0))  # 根据表头索引

# 删除列
del data["note"]

# 遍历除表头外的行
def get_data_list(self):
    _list = []
    for row in self.data:
        _list.append(row)
    return _list
```

## 动态列

<https://xin053.github.io/2016/07/10/tablib%E5%BA%93%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3/>

## 格式转换

- json转xlsx

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
