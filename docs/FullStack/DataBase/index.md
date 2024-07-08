---
comments: true
hide:
  - feedback
---

# 引言

- DBS, DataBase System 数据库系统，即 DBMS + DBA
    - DBA, DataBase Administrator 数据库管理人员
    - DBMS, DataBase Management System 数据库管理系统，即 DB + 管理程序
        - DB, DataBase 数据库，存储数据的集合，即多个数据表

DBMS 排名：<https://db-engines.com/en/ranking>

## 关系型数据库 Relational

RDBMS 建立在实体-关系图（Entity Relationship Diagram, ER）的基础上

ER 图三要素：实体、属性、关系

### 常见的 RDBMS

都是采用的行式存储（Row-based）

- Oracle 1979，第一个商用的 RDBMS，流行后其公司也改名为 Oracle（甲骨文）
- SQL Server 1989，微软开发的商业数据库
- MySQL 1995，开源，MySQL AB 公司创造，然后被 SUN 收购，最后被 Oracle 收购，原创者担心被闭源，然后分支出了 MariaDB
- SQLite 2000，非 C/S 结构的嵌入式数据库，应该是世界上使用最多的数据库，虽然开源但不接受 PR，属于并用于公共领域
- PostgreSQL 企业级开源数据库，适合处理大量复杂查询和数据处理需求
- Snowflake 云原生的数据库存储，适合需要高效、灵活的数据存储和分析能力的企业

### 结构化查询语言 SQL

SQL, Structured Query Language

SQL 是 DBA 与 DBMS 交互的语言，属于一种特定领域专用语言（Domain Specific Language, DSL）

按照功能 SQL 可划分为

- 数据控制语言 DCL(Data Control Language)，用于操作访问权限和安全级别。
- 数据定义语言 DDL(Data Definition Language)，用于定义数据库、数据表、列等数据库对象，创建、删除、修改数据库和表结构。
- 数据操作语言 DML(Data Manipulation Language)，用于增加、删除、修改表中的记录。
- 数据查询语言 DQL(Data Query Language)，用于查询想要的记录，最重要的部分。

### 语法规范

不同 DBMS 有各自的方言，比如大小写不统一

而且同一 DBMS 在不同系统上可能还不一样，比如 MySQL 在 Linux 环境下，数据库名、表名、变量名是严格区分大小写的，而字段名是忽略大小写的，而在 Windows 环境下全部不区分大小写。

所以建议养成习惯

- SQL 保留字、函数名、绑定变量等都大写
- 表名、表别名、字段名、字段别名等都小写
- 字段名以下划线命名

## 非关系型数据库

NoSQL 泛指非关系型数据库，并没有统一的架构和标准语言

之所以出现 NoSQL 是因为随着数据规模越来越大，关系型数据库无法满足快速查询和插入数据的需求

NoSQL 通过降低数据的安全性，减少对事务和复杂查询的支持，以此获取性能上的提升

但随着数据库不断地发展，发现越来越离不开 SQL，所以现在 NoSQL 的 DBMS 也会实现类似 SQL 的功能，而 NoSQL 的含义也以在不断改变

```text
1970：NoSQL = We have no SQL
1980：NoSQL = Know SQL
2000：NoSQL = No SQL!
2005：NoSQL = Not only SQL
2013：NoSQL = No, SQL!
```

每种数据库之间差异较大，各有所长，常见的非关系性数据库有以下几种

### 键值型数据库 Key-value

- Redis
- Memcached

键值型数据库通过 Key-Value 键值的方式来存储数据，其中 Key 和 Value 可以是简单的对象，也可以是复杂的对象。

Key 作为唯一的标识符，优点是查找速度快，在这方面明显优于关系型数据库，同时缺点也很明显，它无法像关系型数据库一样自由使用条件过滤（比如 WHERE），如果你不知道去哪里找数据，就要遍历所有的键，这就会消耗大量的计算。

应用场景：内容缓存、用户信息比如会话、配置信息、购物车等，主要用于处理大量数据的高访问负载。

### 文档型数据库 Document

- MongoDB

文档型数据库，最像 RDBMS 的 NoSQL，以文档的形式储存，类似 JSON，是一系列数据项的集合，一个文档就相当于一条记录。

应用场景：日志、 Web 应用等

### 列存储 Wide column

- Cassandra
- Hbase

列式数据库是将数据按照列存储到数据库中，这样做的好处是可以大量降低系统的 I/O，适合于分布式文件系统，不足在于功能相对有限。

应用场景：日志、 分布式的文件系统(对象存储)、推荐画像、时空数据、消息/订单等。

### 图形数据库 Graph

- Neo4j

图形数据库，利用了图这种数据结构存储了实体对象之间的关系。

最典型的例子就是社交网络中人与人的关系，数据模型主要是以节点和边（关系）来实现，特点在于能高效地解决复杂的关系问题。

应用场景：社交网络，推荐系统等，专注于构建关系图谱。

### 搜索引擎 Search engine

- Elasticsearch
- Splunk
- Solr

搜索引擎也是数据库检索中的重要应用。

虽然关系型数据库采用了索引提升检索效率，但是针对全文索引效率却较低，搜索引擎的优势在于采用了全文搜索的技术，核心原理是倒排索引。
