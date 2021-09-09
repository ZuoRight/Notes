# MySQL

## 概念

- 主键：用来唯一定位记录的字段，通常为与业务无关的自增id
- 外键：

## 连接

```bash
# 启动服务
net start mysql57

# 登陆
mysql -u root -p  # 默认：-h 127.0.0.1 -p 3306

# 退出
exit/quit

# 停止服务
net stop mysql57
```

## 基础

- 库

```SQL
-- 查看有哪些库
SHOW databases;

-- 切换到库中
USE school;

-- 创建库之前先检查是否存在，如果存在则先删除
drop database if exists school;

-- 创建数据库
CREATE DATABASE school;

-- 删除数据库
DROP DATABASE test
```

- 表

```SQL
-- 列出当前库下所有的表
SHOW tables;

-- 查看某表结构
DESC students;
```

## 查

```sql
select * from 表A;
select * from 表A 别名A;
select 字段a 别名a from 表A;
select 字段a, 字段b from 表A;

select 表A.字段a, 表B.字段a from 表A, 表B;

select A.字段a, B.字段a from 表A A inner join 表B B on A.字段a=B.字段a;
```
