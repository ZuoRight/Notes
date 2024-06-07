# SQLite

[SQLite官网](https://www.sqlite.org)

非C/S结构的嵌入式数据库，虽然开源但不接受PR，属于并用于公共领域

> 三大嵌入式数据库：SQLite、Birkeley DB、Firebird

SQLite应该是世界上被使用最多的数据库，通常内置在浏览器、Android和iOS应用、各种编程语言框架中

## GUI

一般支持MySQL的GUI工具也都支持SQLite，比如开源的DBeaver

## 数据类型

SQLite原生支持的类型比较少，不过它支持[灵活类型](https://www.sqlite.org/flextypegood.html)，在Python中可使用适配器扩展自定义类型

```text
SQLite  Python
NULL    None
INTEGER int
REAL    float
TEXT    str(默认)，取决于text_factory
BLOB    bytes
```

## Python操作SQLite

[sqlite3官方文档](https://docs.python.org/zh-cn/3/library/sqlite3.html)

```python
import sqlite3

# 创建连接，如果不存在对应库则新建
conn = sqlite3.connect("demo.db")
# 创建数据库游标
cur = conn.cursor()

# 创建表，可以忽略字段类型(默认N/A)
cur.execute("CREATE TABLE movie(title, year, score)")

# 插入记录
cur.execute("""
    INSERT INTO movie VALUES
        ('Monty Python and the Holy Grail', 1975, 8.2),
        ('And Now for Something Completely Different', 1971, 7.5)
""")
# 插入语句默认会打开一个事务，所以需要提交下事务
conn.commit()

# 插入更多记录
data = [
    ("Monty Python Live at the Hollywood Bowl", 1982, 7.9),
    ("Monty Python's The Meaning of Life", 1983, 7.5),
    ("Monty Python's Life of Brian", 1979, 8.0),
]
cur.executemany("INSERT INTO movie VALUES(?, ?, ?)", data)  # 使用占位符?可以避免SQL注入
conn.commit()

# 查询
res = cur.execute("SELECT year, title FROM movie ORDER BY year")
res.fetchall()  # 获取全部数据行，[(),(),...]
# res.fetchone()  # 获取一条记录
# res.fetchmany(2)  # 获取n条记录

cur.close()  # 关闭游标
conn.close()  # 关闭连接
```
