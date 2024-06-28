
# 增删改查

- Create: Insert
- Delete
- Update
- Retrieve: Select

## 增 Insert

```sql
-- key：顺序不必和表头字段顺序一致，有默认值、可为空、可自增等约束时可不写
-- value：与key的顺序必须一一对应
insert into <表名> (key1, key2, key3, ...) 
VALUES 
    (value1, value2, value3, ...),
    (value1, value2, value3, ...);

-- 将查询结果作为数据插入
INSERT INTO statistics (class_id, average) 
SELECT class_id, AVG(score) 
FROM students 
GROUP BY class_id;

-- 插入记录时如果已存在则更新
INSERT INTO students (id, class_id, name, gender, score)
VALUES (1, 1, '小明', 'F', 99) 
ON DUPLICATE KEY UPDATE name='小明', gender='F', score=99;

-- 插入记录时如果已存在则忽略
INSERT IGNORE INTO students (id, class_id, name, gender, score) 
VALUES (1, 1, '小明', 'F', 99);

-- 替换
REPLACE INTO students (id, class_id, name, gender, score)
VALUES (1, 1, '小明', 'F', 99);
```

## 删 Delete

```sql
-- 清空表，由于安全模式的存在会报错，防止误操作
delete from <表名>;

-- 删除指定条件的行数据
delete from <表名> 
where ...;
```

## 改 Update

```sql
-- 修改主键时要小心
update <表名> 
set key1=value1, key2=value2 
where ...;  -- 不带条件限制时则会更新所有行数据
```

## 查 Select

```sql
-- SELECT可以直接计算
SELECT 1+2;  -- 3

-- 完整语法
SELECT [DISTINCT] 字段列表|*
-- 加 DISTINCT 可以去除重复行
-- * 为通配符
-- 可以接聚合函数
FROM 数据源  -- 数据源可以是表
-- 也可以是其它查询结果（即虚拟表，也叫派生表，或者子查询），必须要用AS起一个别名
WHERE 条件  -- 查询条件
GROUP BY 字段  -- 分组，常与聚合函数一起使用
HAVING 条件  -- 筛选查询结果
ORDER BY 字段A ASC|DESC, 字段B ASC|DESC  -- 默认升序 ASC
-- 先按字段A顺序排，如果有相同的则按字段B顺序排
LIMIT 起始索引，行数  -- 索引0表示从第一行起
```

## 多表查询

```sql
-- 笛卡尔查询
SELECT A.key_a, B.key_b
FROM table_A AS A, table_B AS B;  -- 表可以设置别名方便引用
```

### JOIN

用于连接多个表的列，即字段合并，横向扩展

- 内连接 INNER JOIN

返回符合连接条件的记录，即返回关联表共有的数据，即取交集

可写作 `CROSS JOIN`，或者就简写为 `JOIN`，需要与 `ON` 一起使用

- 外连接 OUTER JOIN

```text
LEFT ~ 返回左表和关联表共有的数据
RIGHT ~ 返回右表和关联表共有的数据
FULL ~ 返回全部的数据，即并集
```

```sql
-- 连接查询，on后面接连接条件
SELECT A.key_a a, B.key_b b, C.key_c c, C.key_d d  -- 字段也可以设置别名方便引用
FROM table_A AS A
JOIN table_B AS B  -- JOIN 默认指内连接 INNER JOIN
JOIN table_C AS C
ON (a=c AND b=d);
```

### UNION

用于连接多个表的行，即记录合并，纵向扩展

`UNION` 与 `UNION ALL` 的区别在于前者会去重，而后者不会去重

## 过滤

- SQ语句执行顺序

```sql
"""
FROM
    WHERE
        JOIN
            GROUP BY
                HAVING
                    SELECT DISTINCT
                        ORDER BY
                            LIMIT
"""
```

从执行顺序可以看出，`WHERE` 是先筛选后连接（即可以先缩小范围再连接），而 `HAVING` 是先连接后筛选，由于 `WHERE` 在连接前缩小了范围，所以效率更高，但 `HAVING` 在 `GROUP BY` 后，可以用来过滤分组，另外还可以接聚合函数。

所以在一些复杂的查询时要善用 `HAVING`，并且可以结合 `WHERE` 提高性能。

### 表达式

```sql
=  -- 等于
<>  -- 不等于
>
>=
<
<=
LIKE _  -- 一个任意字符
LIKE % 多个任意字符  -- 示例，以a开头：a%，以a结尾：%a，包含a：%a%
NOT 条件1
条件1 AND 条件2
条件1 OR 条件2
xx IN ('xxx', 'xx')  -- 比如，HAVING transdate IN ('2015-12-01', '2015-12-03')
```

```sql
SELECT * FROM students WHERE (score < 80 OR score > 90) AND gender = 'M';
```

### 聚合函数

```text
COUNT(*) 计数
SUM() 求和
AVG() 平均值
MAX() 最大值
MIN() 最小值
LEFT(str，n) 返回字符串左边的几个字符
```

```sql
-- 统计查询结果数量
SELECT COUNT(*) <列名>
FROM <表名>;
+-----------+
|   <列名>   |
+------------+
|     10    |
+-----------+

-- 通常与分组一起使用
-- 比如先按班级分组，再按性别分组，然后统计出各班男女生数量各多少
SELECT class_id, gender, COUNT(*) num  -- num 是虚拟字段的别名
FROM students
GROUP BY class_id, gender;  -- 通常分组字段可被写在SELECT后，方便查询结果中区分
```
