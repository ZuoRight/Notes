# MySQL

[官方手册](https://dev.mysql.com/doc/refman/8.0/en/installing.html){ .md-button .md-button--primary }

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
describe students;
-- 删除表
drop table if exist <表名>;
```

![20211030160722](http://image.zuoright.com/20211030160722.png)

字段类型：int、varchar、boolean、datetime

字段值：not null、default x

是否自动增加：auto_increme

是否为主键：primary

- 字段操作

```sql
-- 新增字段
alter table <表名> add column <字段> VARCHAR(10) NOT NULL after <某字段>;
-- 修改字段
alter table <表名> change column <旧字段> <新字段> VARCHAR(20) NOT NULL;
-- 删除字段
alter table <表名> drop column <字段>;
```

## 增删改查

- 增

```sql
-- 值的顺序需要和字段顺序一一对应，字段顺序不必和表头字段顺序一致
-- 如果字段约束可以为空，有默认值，或者是自增约束字段时，则这些字段可以不赋值，否则会报错
insert into <表名> (<key1>, <key2>, <key3>...) values (<value1>, <value2>, <value3>...) (<value1>, <value2>, <value3>...);
```

- 删

```sql
-- 不带条件限制时则会删除所有行数据（安全模式下可能会报错以防止误操作）
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
SELECT *|字段列表
FROM 数据源
WHERE 条件
GROUP BY 字段
HAVING 条件
ORDER BY 字段
LIMIT 起始点，行数
```

```sql
-- 计算功能，可以使用聚合函数
select 1+2;
-- 基本查询
select * from <表名>;
-- 字段和表都可以设置别名，方便引用
select <表别名>.<字段名> <字段别名> from <表名> as <表别名>;
-- 多表连接查询
-- [INNER] JOIN 内连接，取两个表的交集
-- LEFT OUTER JOIN 左连接，取左表都有的
-- RIGHT OUTER JOIN 右连接，取右表都有的
-- FULL OUTER JOIN 全连接，取左右表所有的
select A.<字段a>, B.<字段a> from <表A> A inner join <表B> B on A.<字段a>=B.<字段a>;
```

### WHERE 查询条件

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

### GROUP BY 分组

通常与聚合函数一起使用

`COUNT()` 总数
`SUM()` 总和
`AVG()` 平均值
`MAX()` 最大值
`MIN()` 最小值

```sql
-- 统计各班男生和女生人数
SELECT class_id, gender, COUNT(*) num FROM students GROUP BY class_id, gender;
```

## HAVING 条件

### ORDER BY 排序

- `ASC` 正排，默认主键正排
- `DESC` 倒排

```sql
-- 多级排序，先按xx倒排，相同再按yy正排
ORDER BY xx DESC, yy
```

### LIMIT 限制

`LIMIT 1,5` 表示从第2条数据开始（索引从0开始，1表示第2条），然后共显示5条

### UNION

用于连接两个以上的 SELECT 语句的结果组合到一个结果集合中

## 示例

```sql
-- 把查询结果直接插入到记录中
INSERT INTO 历史流水表 （日结时间字段，其他字段）
SELECT 获取当前时间函数，其他字段
FROM 流水表
```

查询时FROM后面也可以接另一个查询语句

![20211031224626](http://image.zuoright.com/20211031224626.png)

```sql
-- 合并两张字段相同的表时，如果主键值重复时，可使用ON DUPLICATE覆盖处理
INSERT INTO demo.goodsmaster 
SELECT *
FROM demo.goodsmaster1 as a
ON DUPLICATE KEY UPDATE barcode = a.barcode,goodsname=a.goodsname;
-- 运行结果如下
mysql> SELECT *
    -> FROM demo.goodsmaster;
+------------+---------+-----------+---------------+------+------------+
| itemnumber | barcode | goodsname | specification | unit | salesprice |
+------------+---------+-----------+---------------+------+------------+
|          1 | 0001    | 教科书    | 16开          | 本   |      89.00 |
|          2 | 0002    | 笔        | 10支装        | 包   |       5.00 |
|          3 | 0003    | 橡皮      | NULL          | 个   |       3.00 |
|          4 | 0004    | 馒头      |               |      |       1.50 |
+------------+---------+-----------+---------------+------+------------+
4 rows in set (0.00 sec)
-- 另外添加商品表中记录的时候，如果发现itemnumber不连续，可显式指定它的值而不是省略让它自增。
```
