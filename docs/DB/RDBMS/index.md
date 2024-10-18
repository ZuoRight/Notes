---
comments: true
hide:
  - feedback
---

# 关系型数据库

RDBMS 建立在实体-关系图（Entity Relationship Diagram, ER）的基础上

ER 图三要素：实体、属性、关系

## 常见的 RDBMS

都是采用的行式存储（Row-based）

- Oracle 1979，第一个商用的 RDBMS，流行后其公司也改名为 Oracle（甲骨文）
- SQL Server 1989，微软开发的商业数据库
- MySQL 1995，开源，MySQL AB 公司创造，然后被 SUN 收购，最后被 Oracle 收购，原创者担心被闭源，然后分支出了 MariaDB
- SQLite 2000，非 C/S 结构的嵌入式数据库，应该是世界上使用最多的数据库，虽然开源但不接受 PR，属于并用于公共领域
- PostgreSQL 企业级开源数据库，适合处理大量复杂查询和数据处理需求
- Snowflake 云原生的数据库存储，适合需要高效、灵活的数据存储和分析能力的企业
- Hive 建立在Hadoop之上的数据仓库基础设施

## 结构化查询语言 SQL

SQL, Structured Query Language

SQL 是 DBA 与 DBMS 交互的语言，属于一种特定领域专用语言（Domain Specific Language, DSL）

按照功能 SQL 可划分为

- 数据控制语言 DCL(Data Control Language)，用于操作访问权限和安全级别。
- 数据定义语言 DDL(Data Definition Language)，用于定义数据库、数据表、列等数据库对象，创建、删除、修改数据库和表结构。
- 数据操作语言 DML(Data Manipulation Language)，用于增加、删除、修改表中的记录。
- 数据查询语言 DQL(Data Query Language)，用于查询想要的记录，最重要的部分。

## 语法规范

不同 DBMS 有各自的方言，比如大小写不统一

而且同一 DBMS 在不同系统上可能还不一样，比如 MySQL 在 Linux 环境下，数据库名、表名、变量名是严格区分大小写的，而字段名是忽略大小写的，而在 Windows 环境下全部不区分大小写。

所以建议养成习惯

- SQL 保留字、函数名、绑定变量等都大写
- 表名、表别名、字段名、字段别名等都小写
- 字段名以下划线命名
