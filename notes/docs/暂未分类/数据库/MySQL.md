# MySQL

## 概念

- 主键：用来唯一定位记录的字段，通常为与业务无关的自增id
- 外键：

## 查

```sql
select * from 表A;
select * from 表A 别名A;
select 字段a 别名a from 表A;
select 字段a, 字段b from 表A;

select 表A.字段a, 表B.字段a from 表A, 表B;

select A.字段a, B.字段a from 表A A inner join 表B B on A.字段a=B.字段a;
```
