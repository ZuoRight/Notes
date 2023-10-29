# DDL 数据定义语言

## 字符集

```sql
-- 显示字符集相关变量
show variables like '%char%';
'''
存储字符集
    utf8 支持最长三个字节的UTF-8字符
    utf8mb4 支持四字节的UTF-8字符，默认，推荐
排序字符集
    utf8mb4_unicode_ci 基于标准的 Unicode 来排序和比较，能够在各种语言之间精确排序
    utf8mb4_general_ci 8.0前默认，有实现 Unicode 排序规则，在遇到某些特殊语言或者字符集，排序结果可能不一致
    utf8mb4_0900_ai_ci 8.0后默认
        uft8mb4 表示用 UTF-8 编码方案，每个字符最多占 4 个字节
        0900 指的是 Unicode 校对算法版本
        ai 指的是口音不敏感，也就是说，排序时 e，è，é，ê 和 ë 之间没有区别。
        ci 表示不区分大小写，也就是说，排序时 p 和 P 之间没有区别
'''
```

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
```

## 表操作

```sql
-- 列出当前库下所有表
show tables;

-- 创建表
create table <表名> (
    <key1> <key_type> <key_value> 是否自动增加 是否为主键, 
    <key2>...
);

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

字段名称
字段类型
是否允许空值
    不允许：NOT NULL
    允许：默认为 NULL，空值也占用空间
        注意：空字符串长度是0，不属于空值
键
默认值：DEFAULT xxx
附加信息
'''

-- 查看创建表的SQL语句
show create table <表名>;

-- 删除表
drop table if exist <表名>;

-- 对class_id=1的记录进行快照，并存储为新表students_of_class1:
CREATE TABLE students_of_class1 SELECT * FROM students WHERE class_id=1;
```

![20211030160722](http://image.zuoright.com/20211030160722.png)

### 主键

必须可以唯一标识数据表中的记录（`UNIQUE`），且主键不能为空值（`NOT NULL`）

一个表只能有一个主键，可以是一个字段也可以是多个字段的组合（联合主键）

是否为主键：`primary`

是否自动增加：`auto_increme`

### 外键

一个表中的外键对应另一张表的主键，可以重复也可以为空

多表之间的关系

- 一对一
- 一对多
- 多对多

在项目后期，业务量增大的情况下，你需要更多考虑到数据库性能问题，可以取消外键的约束，转移到业务层来实现。而且在大型互联网项目中，考虑到分库分表的情况，也会降低外键的使用。

### 索引

- 普通索引：普通索引只是提升数据检索的速度，并不对字段的唯一性进行约束
- 唯一索引：唯一索引相当于给普通索引加了一个约束，可以为NULL，目的是保证字段的正确性，比如身份证号等
- 主键索引

## 字段类型

合理的定义数据类型，可以提升数据查询和处理速度，反之可能会引发各种错误

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
