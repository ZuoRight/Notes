# 操作SQLite

非C/S结构的嵌入式数据库，虽然开源但不接受PR，属于并用于公共领域

> 三大嵌入式数据库：SQLite、Birkeley DB、Firebird

SQLite应该是世界上被使用最多的数据库，通常内置在浏览器、Android应用、各种编程语言框架中

```python
import sqlite3

# 创建连接，如果不存在对应库则新建
conn = sqlite3.connect("demo.db")
# 创建游标
cur = conn.cursor()

# 执行语句
cur.execute("CREATE TABLE IF NOT EXISTS heros (id int primary key, name text, hp_max real, mp_max real, role_main text)")  # 创建表
cur.execute('insert into heros values(?, ?, ?, ?, ?)', (10000, '夏侯惇', 7350, 1746, '坦克'))  # 插入记录
cur.executemany('insert into heros values(?, ?, ?, ?, ?)',   # 批量插入记录
    ((10000, '夏侯惇', 7350, 1746, '坦克'),
    (10001, '钟无艳', 7000, 1760, '战士'),
    (10002, '张飞', 8341, 100, '坦克'),
    (10003, '牛魔', 8476, 1926, '坦克'),
    (10004, '吕布', 7344, 0, '战士')))

cur.execute("SELECT id, name, hp_max, mp_max, role_main FROM heros")  # 查询
cur.fetchone()  # 获取一条记录
cur.fetchmany(n)  # 获取 n 条记录
cur.fetchall()  # 获取全部数据行

conn.commit()  # 提交事务

cur.close()  # 关闭游标
conn.close()  # 关闭连接
```
