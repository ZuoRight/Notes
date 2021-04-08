# Django

## 创建项目

```python
django-admin startproject
cd startproject
python ./manage.py runserver 0.0.0.0:8000
# 0.0.0.0表示监听所有的IP地址
# 默认使用SQLite数据库，默认创建一个db.sqlite3的文件，可在settings.py中更改数据库引擎和路径
# 访问首页：127.0.0.1:8000
```

```python
# 数据库迁移，产生SQL脚本
python manage.py makemigrations
# 自动生成数据库表
python manage.py migrate
# 创建后台管理员账号
python manage.py createsuperuser
# 根据提示输入用户名邮箱和密码（admin，admin@zuoright.com，123456）
# 访问后台：127.0.0.1:8000/admin
```

加一个应用
再加一个model
