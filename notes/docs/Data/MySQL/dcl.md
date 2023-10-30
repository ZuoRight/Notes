# 数据控制

```sql
-- 查看用户
select user,host from mysql.user;
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
CREATE USER 'qadmin'@'%' IDENTIFIED BY 'passwd';

-- 删除用户
drop user 'test1'@'localhost';

-- 修改用户密码（旧版本修改密码的方式在8.0版本不再适用）
alter user 'root'@'localhost' identified with mysql_native_password by '111111'

-- 查看用户授权信息
show grants for 'qadmin'@'%';

-- 授予所有权限
grant all privileges on *.* to 'qadmin'@'%' with grant option;

-- 刷新系统权限相关的表
flush privileges;

-- 撤销权限
revoke all privileges on *.* from 'qadmin'@'%';
```

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
