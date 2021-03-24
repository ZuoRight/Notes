# 读写csv

## CSV

### 读取

- 普通方式

```python
import csv

with open("test.csv", "r") as csv_file:
    csv_reader = csv.reader(csv_file)
    # 使用next()函数将迭代器先迭代一次，下面再循环将不会输出表头
    headers = next(csv_reader)
    # 输出表头
    """
    print(headers)
    """

    # 获取数据
    for row in csv_reader:
        print(row[0], row[1])
```

- 字典方式（推荐）

```python
import csv

with open("test.csv", "r") as csv_file:
    csv_reader = csv.DictReader(csv_file)
    for row in csv_reader:
        print(row['a'], row['b'])


    """
    # 输出表头
    print(csv_reader.fieldnames)  # ['a', 'b']
    """

    """
    # 以dict形式输出
    result = {}
    for item in csv_reader:
        result[item["a"]] = item["b"]
    print(result)  # {'a1': 'b1', 'a2': 'b2'}
    """
```

### 写入

- 普通方式

```python
import csv

# 如果要追加写入，则以“a”模式打开
# 不加newline每条数据中间会出现空行
with open("test.csv", "w", newline='') as csv_file:
    csv_writer = csv.writer(csv_file)
    # 定义表头
    fieldnames = ["a","b"]
    # 写入表头
    csv_writer.writerow(fieldnames)
    # 写入数据
    csv_writer.writerow(['a1', 'b2'])
```

- 字典方式

```python
import csv

# 如果要追加写入，则以“a”模式打开
# 不加newline每条数据中间会出现空行
with open("test.csv", "w", newline='') as csv_file:
    # 定义表头
    fieldnames = ["a","b"]
    # delimiter参数可指定分隔符，默认为逗号
    csv_writer = csv.DictWriter(csv_file, fieldnames=fieldnames)
    # 写入表头
    csv_writer.writeheader()
    # 写入数据
    csv_writer.writerow({"a":a1, "b":b1})
```