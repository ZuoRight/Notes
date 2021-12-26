# MySQL

[官方手册](https://dev.mysql.com/doc/refman/8.0/en/installing.html){ .md-button .md-button--primary }

## 安装

[官网下载](https://dev.mysql.com/downloads/mysql/)

### Windows

下载后是个压缩包，无需安装，解压后做一些相关配置即可使用

首先在解压后文件的目录下，新建一个配置文件：`my.ini`，然后设置以下内容

```ini
[client]
# 设置mysql客户端默认字符集
default-character-set=utf8
 
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录，注意，这里要用两个反斜线\\
basedir=C:\\path\\mysql-8.0.27-winx64
# 设置 mysql数据库的数据的存放目录，MySQL 8+ 不需要以下配置，系统自己生成即可，否则有可能报错
# datadir=C:\\path\\sqldata
# 允许最大连接数
max_connections=20
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```

然后设置环境变量

- `MYSQL_HOME`: `安装路径`
- `Path`: `%MYSQL_HOME%\bin`

然后以管理员身份打开命令行，输入：`mysqld --initialize --console`，初始化完毕后会生成一个初始密码（记得保存）

然后输入：`mysqld install`，注册服务，成功后即可启动服务登录使用

### Mac

下载后安装一直下一步即可，最后会默认创建root账户，让你设置一个密码，加密方式建议选：`Use Legacy Password Encryption`（不然某些第三方客户端连接数据库时会报错）

然后配置环境变量

```bash
cd ~
vim ./.bash_profile
export PATH=$PATH:/usr/local/mysql/bin
export PATH=$PATH:/usr/local/mysql/support-files
source ~/.bash_profile
```

## 连接

```bash
net start mysql  # 启动服务
net stop mysql  # 停止服务

# 连接服务
mysql [-h 127.0.0.1] -u root -p
# -h 默认主机名为本机127.0.0.1
# -P 默认端口号3306
# -u 指定用户名
# -p 带此参数表明需要密码，反之不需要，回车后密文输入

# 断开连接
exit
# 或者
quit
```

## 修改密码

> 注意：旧版本修改密码的方式在8.0版本不再适用

```sql
use mysql
alter user 'root'@'localhost' identified with mysql_native_password by '111111'
-- 刷新系统权限相关的表
flush privileges;
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
desc students;  -- describe
-- 删除表
drop table if exist <表名>;

-- 对class_id=1的记录进行快照，并存储为新表students_of_class1:
CREATE TABLE students_of_class1 SELECT * FROM students WHERE class_id=1;
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

having与where作用相同，但where后面不能接聚合函数，所以GROUP BY分组之后，通常使用having来对分组记录进行筛选

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

## 示例

```sql
-- 把查询结果直接插入到记录中
INSERT INTO 历史流水表 （日结时间字段，其他字段）
SELECT 获取当前时间函数，其他字段
FROM 流水表
```

查询时FROM后面也可以接另一个查询语句

![20211031224626](http://image.zuoright.com/20211031224626.png)

## 索引 key

把表中某列的值存储到某种数据结构中，就叫索引

> 最常用的数据结构为：B-Tree

优点

- 提高数据检索效率，降低数据库IO成本
- 通过索引对数据进行排序，降低CPU消耗

缺点

- 占用空间
- 降低了表更新的速度
- 建立最优索引需要花时间研究

适合建立索引的字段：主键，外键，频繁作为查找或排序的字段

> 索引的效率取决于索引列的值是否散列，即该列的值如果越互不相同，那么索引效率越高，像性别这种列有索引反而会降低效率，这种情况下大数据中会使用分组的概念

## 性能优化

对索引字段做函数操作，或有隐式类型转换涉及函数操作，或有隐式字符编码转换，可能会破坏索引值的有序性，因此优化器就决定放弃走树搜索功能，导致全索引扫描。

因此用`explain`+`SQL语句`分析一下，是一个很好的习惯，它可以模拟优化器执行SQL查询语句。

## 事务 transaction

> <https://www.liaoxuefeng.com/wiki/1177760294764384/1179611198786848>

数据库事务是对数据库进行一系列操作的序列，这些操作要么全部执行，要么全不执行，是一个不可分割的集合。

具有ACID这4个特性：

- A：Atomic，原子性，将所有SQL作为原子工作单元执行，要么全部执行，要么全部不执行；
- C：Consistent，一致性，事务完成后，所有数据的状态都是一致的，即A账户只要减去了100，B账户则必定加上了100；
- I：Isolation，隔离性，如果有多个事务并发执行，每个事务作出的修改必须与其他事务隔离；
- D：Duration，持久性，即事务完成后，对数据库数据的修改被持久化存储。

SQL标准定义了4种隔离级别，分别对应可能出现的数据不一致的情况

![20211103154025](http://image.zuoright.com/20211103154025.png)

- 脏读，一个事务会读到另一个事务更新后但未提交的数据，如果另一个事务回滚，那么当前事务读到的数据就是脏数据。
- 不可重复读，在一个事务内，多次读同一数据，在这个事务还没有结束时，如果另一个事务恰好修改了这个数据，那么，在第一个事务中，两次读取的数据就可能不一致。
- 幻读，在一个事务中，第一次查询某条记录，发现没有，但是，当试图更新这条不存在的记录时，竟然能成功，并且，再次读取同一条记录，它就神奇地出现了。

```sql
-- 指定隔离级别，MySQL中如果使用InnoDB，默认的隔离级别是Repeatable Read。
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
BEGIN;  -- 开启事务
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;  -- 提交事务
ROLLBACK;  -- 回滚事务
```

![20211103162720](http://image.zuoright.com/20211103162720.png)
