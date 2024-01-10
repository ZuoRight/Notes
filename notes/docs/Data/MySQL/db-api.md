# DB-API

Python 操作 MySQL 数据库需要遵守 DB-API 规范，有很多封装好的驱动库供我们选择

![20220809200749](http://image.zuoright.com/20220809200749.png)

步骤

1. 引入 API 模块
2. 与数据库建立连接
3. 执行 SQL 语句
4. 关闭数据库连接

## MySQLdb

- MySQLdb 1，依赖了mysql-connector-c，仅支持Py2.x，`pip install MySQL-python`
- MySQLdb 2，即 `mysqlclient`，从MySQLdb 1 fork而来，适配了Py3.x并修复了一些问题，性能最好，Django推荐

> 参考官方文档：<https://mysqlclient.readthedocs.io/user_guide.html#mysqldb>
>
> 安装比较麻烦，参考README：<https://github.com/PyMySQL/mysqlclient>

```shell
# Mac
brew install mysql-client
pip install mysqlclient
"""
如果报NameError: name ‘_mysql’ is not defined
可以添加环境变量：export DYLD_LIBRARY_PATH="/usr/local/mysql/lib:$PATH"
"""

# Ubuntu
pip install mysqlclient
'''
如果报NameError: name ‘_mysql’ is not defined
    sudo apt-get install python3-dev default-libmysqlclient-dev build-essential
在docker容器（python:3.9）中实际测试发现貌似并不需要先执行此行命令
另外apt-get list --installed 发现是有 default-libmysqlclient-dev 这个依赖包的
'''
```

```python
import MySQLdb

conn = MySQLdb.connect(
    host = "localhost",
    port = 3306,  # int类型
    user = "root",
    passwd = "12345678",
    db = "cases"
)

cur = conn.cursor()  # 创建游标

exchange = "deribit"
subject = "SWAP_USD"
currency = "BTC"
# 注意：字段值要被''包裹
sql = f"""SELECT * FROM onestep_{exchange} WHERE subject='{subject}' and currency='{currency}'"""

r = cur.execute(sql)

print(r)  # 6，返回结果数量
print(cur.fetchone())  # <class 'tuple'>
print(cur.fetchmany(2))
print(cur.fetchall())  # cur是一个迭代器，这里把结果都取完，则后面再取就是空的()
print(cur.fetchmany(2))

# 同时插入多条记录用executemany
sql = "INSERT INTO student VALUES(%s,%s,%s,%s)"
cur.executemany(sql,
    [
        ('3','Tom','1 year 1 class','6'),
        ('3','Jack','2 year 1 class','7'),
        ('3','Yaheng','2 year 2 class','7'),
    ]
)
conn.commit()  # 修改/插入后需要提交才能生效
cur.close()  # 关闭游标
conn.close()  # 关闭数据库
```

## mysql-connector

`pip install mysql-connector`

MySQL官方提供的，纯Python实现，所以性能很差，且使用人数少

```python
import mysql.connector

# 打开数据库连接
db = mysql.connector.connect(
       host="localhost",
       user="root",
       passwd="12345678",
       database='demo', 
    #    auth_plugin='mysql_native_password'  # 密码验证方式，采用明文，8.0版本为caching_sha2_password
)
# 获取操作游标 
cursor = db.cursor()
# 执行SQL语句
cursor.execute("SELECT VERSION()")
# 获取一条数据
data = cursor.fetchone()
print("MySQL版本: %s " % data)

# 关闭游标&数据库连接
cursor.close()
db.close()
```

## PyMySQL

`pip install PyMySQL`

```shell
RuntimeError: 'cryptography' package is required for sha256_password or caching_sha2_password auth methods
# 如果使用 PyMySQL 遇到如上问题
# 是因为 mysql8.0 后身份认证插件由 mysql_native_password 变为 caching_sha2_password
# 需要安装 cryptography 来解决：pip3 install cryptography
```

也是纯Python实现，性能最差，但最流行，如果项目里用了gevent貌似只能用pymysql

```python
import pymysql

class RunSql():
    __host="localhost"
    __port=3306
    __user="root"
    __password="passwd"

    def __init__(self, db, sql):
        # 创建连接
        self.__conn = pymysql.connect(
            host=self.__host, 
            port=self.__port, 
            user=self.__user, 
            password=self.__password, 
            db=db, 
            charset="utf8"
        )
        # 创建游标
        self.__cursor = self.__conn.cursor()
        # 将多条语句在分号处拆散成多个单条语句，并去除末尾分号可能产生的空语句
        command_list = [i.strip() for i in sql.split(";") if i.strip() != '']
        # print(command_list)
        # 执行语句
        for command in command_list:
            try:
                self.__cursor.execute(command)
                self.__conn.commit()
            # 发生错误时回滚
            except Exception as e:
                self.__conn.rollback()
                print(e)
        # 关闭游标
        self.__cursor.close()
        # 关闭连接
        self.__conn.close()


    # 获取第一行结果
    def select_row_one(self):
        row_one = self.__cursor.fetchone()
        return row_one

    # 获取所有结果
    def select_row_all(self):
        row_all = self.__cursor.fetchall()
        return row_all

    # 获取表头
    def desc(self):
        desc = self.__cursor.description
        if desc:
            head = [item[0] for item in desc]
        else:
            head = []
        return head
```

## ORM

- SQLALchemy
- Django Admin
- Peewee

### SQLALchemy

<https://docs.sqlalchemy.org/en/14/index.html>

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String


mysql_config = {"user": xxx, "password": xxx, "host": xxx, "port": xxx, "database": xxx}
mysql_uri = 'mysql+pymysql://{user}:{password}@{host}:{port}/{database}'.format(**mysql_config)
engine = create_engine(mysql_uri, encoding="utf8mb4")
session = sessionmaker(bind=engine)()

Base = declarative_base()
class User(Base):
    __tablename__ = 'users'  # 映射的表名
    id = Column(Integer, primary_key=True)
    name = Column(String(255))
    age = Column(Integer)


# 返回所有数据
session.query(User).all()
# 查询满足条件的所有数据
session.query(User).filter_by(name="Tony").all()
# 查询满足条件的首条数据
res = session.query(User).filter_by(name="Tony").first()  # <User(user='xxx', name='Tony', xxx='xxx')>
res.name  # Tony


# 或者
from sqlalchemy import select
stmt = select(User).where(User.name == "Tony")
result = session.execute(stmt)  # <sqlalchemy.engine.result.ChunkedIteratorResult object at 0x1060f3670>
result.scalars()  # <sqlalchemy.engine.result.ScalarResult object at 0x105f6bca0>
result.scalars().all()  # [<User(user='xxx', name='Tony', xxx='xxx')>]
for row in result.scalars():
    print(f"{row.user} {row.name}")
```