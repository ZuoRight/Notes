# 引言

数据结构是计算机中存储和组织数据的方式，使数据可以得到有效利用，算法是在有限时间内解决特定问题的一组指令或操作步骤。

```text
类比1
数据类型 ≈ 不同材料的积木，比如塑料、木头、金属等
数据结构 ≈ 积木组织形式，包括形状、大小、连接方式等
算法 ≈ 把积木拼成不同模型的一系列操作步骤

类比2
数据类型 ≈ 不同类别的书籍，比如期刊、小说、教辅等
数据结构 ≈ 书籍的整理方式，比如书架、抽屉、台子等
算法 ≈ 获取书籍的方式
```

使用数据结构时需要思考的事情

- 存储什么：某一种数据结构可能最适合某一个数据类型
- 操作成本：让高频操作成本降到最低
- 内存消耗：尽可能减少内存的使用
- 易于实现

---

- DBS, DataBase System 数据库系统，即 DBMS + DBA
    - DBA, DataBase Administrator 数据库管理人员
    - DBMS, DataBase Management System 数据库管理系统，即 DB + 管理程序
        - DB, DataBase 数据库，存储数据的集合，即多个数据表

DBMS 排名：<https://db-engines.com/en/ranking>

根据数据模型分类

- 关系 RDBMS
- 非关系 NoSQL

根据处理类型

- OLTP 在线事务处理，适用于管理日常事务的数据库，比如 MySQL
- OLAP 在线分析处理，适用于数据分析和复杂查询的数据库，比如 Hive

---

每碰到一个新数据库，我们需要先关注它的数据模型，这是它底层存储的核心，这样才能从理论上分析出这个数据库的适用场景

实现索引的方式有很多种

- 哈希表，因为范围查询麻烦，只适用于有等值查询的场景，比如 Memcached 及其他一些 NoSQL 引擎
- 有序数组，因为插入数据比较麻烦，只适用于静态存储引擎
- N 叉树，应用广泛
- B-Tree
- 字典树
- 跳表

