# MySQL

```bash
net start mysql  # 启动服务
net stop mysql  # 停止服务

# 连接服务
# 默认host：-h 127.0.0.1
# 默认端口号：-p 3306
mysql -u root -p  # exit或quit断开连接
```

## 基础操作

- 库操作

``

```sql
-- 检查编码
show variables like '%char%';
-- 查看有哪些库
show database;
-- 删除库
drop database if exists <库名>;
-- 创建库
create database <库名>;
-- 创建库，并指定编码格式和校验集
create database if not exists <库名> default character set utf8 collate utf8_general_ci;
-- 使用库
use <库名>;
```

- 表操作

```sql
-- 列出当前库下所有表
show tables;
-- 查看创建表的SQL语句
show create table <表名>;
-- 创建表
create table <表名> (<key1> <key_type> <key_value> 是否自动增加 是否为主键, <key2>...);
-- 查看表结构
desc students;
-- 删除表
drop table if exist <表名>;
```

![20211030160722](http://image.zuoright.com/20211030160722.png)

字段类型：int、varchar、boolean、datetime

字段值：not null、default x

是否自动增加：auto_increme

是否为主键：primary

## 增删改查

- 增

```sql
-- 值的顺序需要和字段顺序一一对应，字段顺序不必和表头字段顺序一致
insert into <表名> (<key1>, <key2>, <key3>...) values (<value1>, <value2>, <value3>...) (<value1>, <value2>, <value3>...);
```

- 删

```sql
-- 不带条件限制时则会删除所有行数据
delete from <表名> where ... and ...;
```

- 改

```sql
-- set后面可以用表达式
-- 不带条件限制时则会更新所有行数据
update <表名> set key1=value1, key2=value2 where ... and ...;
```

- 查

```sql
-- 计算功能，可以使用聚合函数
select 1+2;
-- 基本查询
select * from <表名>;
-- 字段和表都可以设置别名，方便引用
select <表别名>.<字段名> <字段别名> from <表名> <表别名>;
-- 多表连接查询
-- [INNER] JOIN 内连接，取两个表的交集
-- LEFT OUTER JOIN 左连接，取左表都有的
-- RIGHT OUTER JOIN 右连接，取右表都有的
-- FULL OUTER JOIN 全连接，取左右表所有的
select A.<字段a>, B.<字段a> from <表A> A inner join <表B> B on A.<字段a>=B.<字段a>;
```

## 聚合函数

`COUNT()` 总数
`SUM()` 总和
`AVG()` 平均值
`MAX()` 最大值
`MIN()` 最小值

## WHERE 表达式

- `=` 等于
- `<>` 不等于
- `>`
- `>=`
- `<`
- `<=`
- `LIKE _` 一个任意字符
- `LIKE %` 多个任意字符，示例：以a开头`a%`、以a结尾`%a`、包含`%a%`
- `NOT <条件1>`
- `<条件1> AND <条件2>`
- `<条件1> OR <条件2>`
