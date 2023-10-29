# 操作

## DCL 数据库控制语言

```sql
-- 使用/切换表
use mysql;

-- 查看用户
select user,host from user;
'''
+------------------+-----------+
| user             | host      |
+------------------+-----------+
| demo             | %         |
| root             | %         |
| mysql.infoschema | localhost |
| mysql.session    | localhost |
| mysql.sys        | localhost |
+------------------+-----------+
5 rows in set (0.00 sec)
'''

-- 创建用户（%表示所有IP都可以访问）
CREATE USER 'qadmin'@'%' IDENTIFIED BY 'qadminpasswd';
-- 授予所有权限
grant all privileges on *.* to 'qadmin'@'%' with grant option;
-- 刷新系统权限相关的表
flush privileges;

-- 查看用户授权信息
show grants for 'qadmin'@'%';
-- 撤销权限
revoke all privileges on *.* from 'qadmin'@'%';

-- 修改密码（旧版本修改密码的方式在8.0版本不再适用）
alter user 'root'@'localhost' identified with mysql_native_password by '111111'

-- 删除用户
drop user 'test1'@'localhost';
```

## DML 数据操作语言

### 增

```sql
-- 值的顺序需要和字段顺序一一对应，字段顺序不必和表头字段顺序一致
-- 如果字段约束可以为空，有默认值，或者是自增约束字段时，则这些字段可以不赋值，否则会报错
insert into <表名> (<key1>, <key2>, <key3>...) values (<value1>, <value2>, <value3>...) (<value1>, <value2>, <value3>...);

-- 插入或替换
-- 如果我们希望插入一条新记录（INSERT），但如果记录已经存在，就先删除原记录，再插入新记录。此时，可以使用REPLACE语句，这样就不必先查询，再决定是否先删除再插入：
REPLACE INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99);

-- 插入或更新
-- 如果我们希望插入一条新记录（INSERT），但如果记录已经存在，就更新该记录，此时，可以使用INSERT INTO ... ON DUPLICATE KEY UPDATE ...语句：
INSERT INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99) ON DUPLICATE KEY UPDATE name='小明', gender='F', score=99;

-- 插入或忽略
-- 如果我们希望插入一条新记录（INSERT），但如果记录已经存在，就啥事也不干直接忽略，此时，可以使用INSERT IGNORE INTO ...语句：
INSERT IGNORE INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99);

-- 查询并插入
INSERT INTO statistics (class_id, average) SELECT class_id, AVG(score) FROM students GROUP BY class_id;

-- 另外添加记录的时候，如果发现不连续，可显式指定它的值而不是省略让它自增。
```

### 删

```sql
-- 不带条件限制时则会删除所有行数据（安全模式下可能会报错以防止误操作）
delete from <表名> where ... and ...;
```

### 改

```sql
-- set后面可以用表达式
-- 不带条件限制时则会更新所有行数据
update <表名> set key1=value1, key2=value2 where ... and ...;
```

## DQL 数据查询语言

### 顺序

- 语句顺序

```sql
-- DISTINCT 去除重复行
SELECT DISTINCT *|字段列表 ... as ...

FROM 数据源
WHERE 条件
GROUP BY 字段
HAVING 条件

ORDER BY 字段A ASC|DESC, 字段B
LIMIT 起始点，行数
```

- 执行顺序

```sql
FROM
WHERE
GROUP BY
HAVING

SELECT DISTINCT

ORDER BY
LIMIT
```

### 查

```sql
-- 计算功能，可以使用聚合函数
select 1+2;
-- 基本查询
select * from <表名>;
-- 多表查询：笛卡尔查询，字段和表都可以设置别名，方便引用
select <表1别名>.<字段名> <字段别名>, <表2别名>.<字段名> from <表1名> <表1别名>, <表2名> <表2别名>;
-- 多表查询：连接查询，on后面接连接条件
select A.<字段a>, B.<字段a> from <表A> A inner join <表B> B on A.<字段a>=B.<字段a>;
```

![20211103142215](http://image.zuoright.com/20211103142215.png)

join用于连接多个表的列，即字段合并，横向扩展，而union用于连接多个表的行，即记录合并，纵向扩展

`union`与`union all`的区别在于前者会去重，而后者不会去重

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

```sql
SELECT * FROM students WHERE (score < 80 OR score > 90) AND gender = 'M';
```

HAVING 与 WHERE 都是过滤用的，WHERE 是过滤行，HAVING 过滤组，HAVING 支持所有 WHERE 的操作，后面可以接聚合函数

### GROUP BY 分组

通常与聚合函数一起使用

`COUNT()` 计数
`SUM()` 求和
`AVG()` 平均值
`MAX()` 最大值
`MIN()` 最小值
`LEFT(str，n)` 返回字符串左边的几个字符

```sql
-- 统计查询结果行数，COUNT(*) 等价于 COUNT(id)
SELECT COUNT(*) <列名> FROM <表名>;
+-----------+
|   <列名>   |
+------------+
|     10    |
+-----------+
-- 统计各班男生和女生人数
SELECT class_id, gender, COUNT(*) num FROM students GROUP BY class_id, gender;
```

### ORDER BY 排序

- `ASC` 正排，默认主键正排
- `DESC` 倒排

```sql
-- 多级排序，先按xx倒排，相同再按yy正排
ORDER BY xx DESC, yy
```

### LIMIT 限制

`LIMIT 1,5` 表示从第2条数据开始（索引从0开始，1表示第2条），然后共显示5条
