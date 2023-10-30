# 数据定义

## 库操作

```sql
-- 查看有哪些库
show databases;
'''
+--------------------+
| Database           |
+--------------------+
| mysql              |  主要保存服务器运行时需要的系统信息，比如数据文件夹、当前使用的字符集、约束检查信息等
| information_schema |  主要保存服务器系统信息，比如数据库名、表名、字段名、存取权限等
| performance_schema |  监控各类性能指标
| sys                |  更容易理解的方式展示性能指标供开发人员监控（图形化客户端中仅显示这个）
+--------------------+
'''

-- 创建库，可以指定指定字符集和校对规则等
CREATE DATABASE <库名> 
    CHARACTER SET utf8mb4 
    COLLATE utf8mb4_general_ci;

-- 删除库
drop database if exists <库名>;

-- 使用库
use <库名>;

-- 列出当前库下所有表
show tables;
```

## 表操作

```sql
-- 创建表
CREATE TABLE <表名> (
    <key1> <key_type> <key_value> [字段级别约束] [默认值], 
    <key2> ...,
    ......
    [表级别约束]
);
'''
字段名称，需要避开系统关键字，比如：value
字段类型
字段值
字段级别约束
默认值：DEFAULT xxx
表级别约束
'''

-- 查看创建表的SQL语句
show create table <表名>;

-- 删除表
drop table if exist <表名>;

-- 查看表结构
describe students;  -- 可简写为 desc
'''
mysql> DESCRIBE demo.test;
+-----------+------+------+-----+---------+-------+
| Field     | Type | Null | Key | Default | Extra |
+-----------+------+------+-----+---------+-------+
| barcode   | text | YES  |     | NULL    |       |
| goodsname | text | YES  |     | NULL    |       |
| price     | int  | YES  |     | NULL    |       |
+-----------+------+------+-----+---------+-------+
3 rows in set (0.00 sec)
'''

-- 对class_id=1的记录进行快照，并存储为新表students_of_class1:
CREATE TABLE students_of_class1 SELECT * FROM students WHERE class_id=1;
```

![20211030160722](http://image.zuoright.com/20211030160722.png)

- 修改表

```sql
-- 新增字段
ALTER TABLE <表名> ADD column <字段名> VARCHAR(10) NOT NULL after <某字段>;

-- 修改字段
ALTER TABLE <表名> CHANGE COLUMN <旧字段> <新字段> VARCHAR(20) NOT NULL;

-- 修改字段名
ALTER TABLE <表名> RENAME COLUMN <旧字段名> to <新字段名>;

-- 修改字段类型
ALTER TABLE <表名> MODIFY COLUMN <字段名> float(3,1);

-- 删除字段
ALTER TABLE <表名> DROP column <字段名>;
```

## 字段类型

合理的定义数据类型，可以提升数据查询和处理速度，反之可能会引发各种错误

### 整数

![20231029212236](https://image.zuoright.com/20231029212236.png)

若为了节省存储空间而选用占用字节数更少的类型，一旦因取值范围而产生系统故障，则得不偿失，所以应该首先保证不会超过取值范围，其次再考虑节省空间的问题，最常用的是 `INT`

### 浮点数

![20231029212955](https://image.zuoright.com/20231029212955.png)

- `FLOAT` 单精度浮点数
- `DOUBLE` 双精度浮点数
- `REAL`，默认为 DOUBLE，也可以设置为 FLOAT：`SET sql_mode="REAL_AS_FLOAT";`

由于浮点数在计算机中需要转换为二进制形式存储，小数部分会有无法精准转换的问题，所以浮点数存储是不精确的

### 定点数

定点数在存储时会把十进制的整数和小数部分拆分开，并转为十六进制存储，所以不会存在精度损失的问题

所以对精度要求高的业务场景通常使用 `DECIMAL` 类型存储，不过定点数需要更多的存储空间

```sql
ALTER TABLE demo.goodsmaster MODIFY COLUMN price DECIMAL(5,2);
-- DECIMAL(M,D)
--     M 表示整数加小数的位数
--     D 表示小数的位数，所以 M>D
```

### 字符串

- TEXT 按照实际长度存储
- CHAR(M) 固定长度字符串
- VARCHAR(M) 可变长度字符串
- ENUM 枚举，取1个
- SET 字符串对象，取0或多个

其中 CHAR、VARCHAR 需要预先知道字符长度，SET 和 ENUM 需要预先知道所有可能的取值

TEXT 类型最为灵活和常用，但因为长度不固定所以不能作为主键

TEXT 根据长度不同又可分为

![20231030000937](https://image.zuoright.com/20231030000937.png)

### 日期与时间

![20231030001406](https://image.zuoright.com/20231030001406.png)

TIME 的取值范围不是 `-23:59:59～23:59:59`，目的是可以 TIME 来表示时间间隔，而不只是一天的时间

最常用的是 DATETIME

## 约束

约束限定了表中数据应该满足的条件，MySQL会根据这些限定条件对表进行监控，阻止破坏约束条件的操作执行，并提示错误，从而确保表中的数据的唯一性、合法性、完整性。

### 非空约束

即是否允许空值

默认允许，空值 NULL 也占用空间，空字符串长度是0，不属于空值

不允许则设置为：`NOT NULL`

### 唯一性约束

即字段的值不能重复，可以指定多个唯一字段，可以为空值

如果唯一则设置为：`UNIQUE`

### 主键约束

必须可以唯一标识数据表中的记录，且不能为空值

一个表只能有一个主键，但可以是一个字段也可以是多个字段的组合（联合主键）

如果为主键则设置为：`PRIMARY`

### 外键约束

一个表中的外键对应另一张表的主键，可以重复也可以为空

### 自增约束

只有整数类型的字段可以被设置自增约束

当没有给自增约束字段设置值的时候，它会在已有的最大值基础之上自动加1作为值

如果自动增加则设置为：`AUTO_INCREME`

### 默认值约束

给字段设置默认值：`DEFAULT xxx`
