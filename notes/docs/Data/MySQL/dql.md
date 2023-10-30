# 数据查询

## 顺序

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

## 查

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

## 示例

查询时FROM后面也可以接另一个查询语句

![20211031224626](http://image.zuoright.com/20211031224626.png)