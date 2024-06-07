# CSV

Comma-Separated Values 逗号分隔值，也称为字符分隔值

- 读

```python
import csv

# 普通方式
with open("test.csv", "r") as csv_file:
    csv_reader = csv.reader(csv_file)
    headers = next(csv_reader)  # 使用next()函数将迭代器先迭代一次，跳过表头
    for row in csv_reader:
        print(row[0], row[1])


# 字典方式（推荐）
with open("test.csv", "r") as csv_file:
    csv_reader = csv.DictReader(csv_file)
    # print(csv_reader.fieldnames)  # 输出表头：['a', 'b']
    result = {}
    for item in csv_reader:
        result[item["a"]] = item["b"]
    print(result)  # {'a1': 'b1', 'a2': 'b2'}
```

- 写

```python
import csv

# 普通方式
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

# 字典方式
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
