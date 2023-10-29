# 数据库

- DB(DataBase) 数据库，存储数据的集合，即多个数据表
- DBMS(DataBase Management System) 数据库管理系统，`DBMS = DBs + 管理程序`，比如MySQL、Redis等
- DBA(DataBase Administrator) 数据库管理人员
- DBS(DataBase System) 数据库系统，`DBS = DB + DBMS + DBA`

## 关系型数据库

关系型数据库（RDBMS）是建立在实体-关系图（Entity Relationship Diagram）基础上的数据库，主流的有

> ER图三要素：实体、属性、关系

- `Oracle` 1979，第一个商用的RDBMS，流行后其公司也改名为Oracle（甲骨文）
- `SQL Server` 1989，微软开发的商业数据库
- `MySQL` 1995，开源，MySQL AB公司创造，然后被SUN收购，最后被Oracle收购，原创者担心被闭源，然后分支出了MariaDB
- `SQLite` 2000，非C/S结构的嵌入式数据库，应该是世界上使用最多的数据库，虽然开源但不接受PR，属于并用于公共领域

### SQL

结构化查询语言（Structured Query Language），是DBA与关系型数据库交互的语言，SQL语言按照功能可划分为

- `DCL`(Data Control Language)，数据控制语言，用于操作访问权限和安全级别。
- `DDL`(Data Definition Language) 数据定义语言，用于定义数据库、数据表、列等数据库对象，创建、删除、修改数据库和表结构。
- `DML`(Data Manipulation Language)，数据操作语言，用于增加、删除、修改表中的记录。
- `DQL`(Data Query Language)，数据查询语言，用于查询想要的记录，最重要的部分。

不同DBMS有各自的方言（语法规范），比如大小写不统一，而且同一DBMS在不同系统上可能还不一样，比如MySQL在Linux的环境下，数据库名、表名、变量名是严格区分大小写的，而字段名是忽略大小写的。而MySQL在Windows的环境下全部不区分大小写。所以建议：

- SQL保留字、函数名、绑定变量等都大写
- 表名、表别名、字段名、字段别名等都小写
- 字段名以下划线命名

## 非关系型数据库

NoSQL 泛指非关系型数据库，并没有统一的架构和标准语言，每种数据库之间差异较大，各有所长

随着数据库不断地发展，发现越来越离不开SQL，所以现在NoSQL的DBMS也会实现类似SQL的功能，而 NoSQL 的含义也以不断改变。

```text
1970：NoSQL = We have no SQL
1980：NoSQL = Know SQL
2000：NoSQL = No SQL!
2005：NoSQL = Not only SQL
2013：NoSQL = No, SQL!
```

- 键值型数据库：Redis、Memcached

键值型数据库通过 Key-Value 键值的方式来存储数据，其中 Key 和 Value 可以是简单的对象，也可以是复杂的对象。Key 作为唯一的标识符，优点是查找速度快，在这方面明显优于关系型数据库，同时缺点也很明显，它无法像关系型数据库一样自由使用条件过滤（比如 WHERE），如果你不知道去哪里找数据，就要遍历所有的键，这就会消耗大量的计算。键值型数据库典型的使用场景是作为内容缓存。Redis 是最流行的键值型数据库。

- 文档型数据库：MongoDB

文档型数据库，最像RDBMS的NoSQL，用来管理文档，在数据库中文档作为处理信息的基本单位，一个文档就相当于一条记录，MongoDB 是最流行的文档型数据库。

- 搜索引擎：Elasticsearch、Splunk、Solr

搜索引擎也是数据库检索中的重要应用，常见的全文搜索引擎有 Elasticsearch、Splunk 和 Solr。虽然关系型数据库采用了索引提升检索效率，但是针对全文索引效率却较低。搜索引擎的优势在于采用了全文搜索的技术，核心原理是“倒排索引”。

- 列存储：Hbase

列式数据库是相对于行式存储的数据库，Oracle、MySQL、SQL Server 等数据库都是采用的行式存储（Row-based），而列式数据库是将数据按照列存储到数据库中，这样做的好处是可以大量降低系统的 I/O，适合于分布式文件系统，不足在于功能相对有限。

- 图形数据库：社交网络

图形数据库，利用了图这种数据结构存储了实体（对象）之间的关系。最典型的例子就是社交网络中人与人的关系，数据模型主要是以节点和边（关系）来实现，特点在于能高效地解决复杂的关系问题。
