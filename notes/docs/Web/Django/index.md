# Django

[Django的官方文档(有中文版)](https://docs.djangoproject.com/zh-hans){ .md-button .md-button--primary }

Django采用了MTV的框架模式，即：Model(模型)，Template(模版)，View(视图)

## 项目

- 创建

```shell
mkdir mysite
cd mysite

# Django 4.0+ 依赖 Python 3.8+
python -m venv env
pip install django

# 初始化项目，注意结尾的点
django-admin startproject mysite .
```

- 结构

```shell
# 项目容器，可重命名
mysite/
    manage.py  # 命令行工具
    # 项目
    mysite/
        __init__.py
        settings.py  # 配置文件
        urls.py  # 路由声明，网站目录
        asgi.py  # 兼容asgi的服务器入口
        wsgi.py  # 兼容wsgi的服务器入口
```

## 应用

一个项目可以包含多个应用，一个应用可以被多个项目使用

- 创建

应用可以存放在任意路径：`django-admin startapp <app_name>`

不过通常与manage.py同级，这样就可以作为顶级模块导入：`python manage.py startapp <app_name>`

- 结构

```shell
one_app/
    __init__.py
    migrations/
        __init__.py
    admin.py
    apps.py
    models.py
    tests.py
    views.py
    # URLconf路由配置文件（也可以是其他任何路径）
    urls.py
    # 模版容器
    templates/
        # 命名空间，由于DjangoTemplates会在每个INSTALL_APPS路径下依次寻找templates子目录，如果多个APP之间模板名称一样，只会匹配到第一个，容易出错
        one_app/
            index.html  # 模版
```

- 声明

```python
# mysite/settings.py

INSTALLED_APPS = [
    # Django自带应用
    'django.contrib.admin',  # 管理后台
    'django.contrib.auth',  # 认证授权系统
    'django.contrib.contenttypes',  # 内容类型框架
    'django.contrib.sessions',  # 会话框架
    'django.contrib.messages',  # 消息框架
    'django.contrib.staticfiles',  # 管理静态文件的框架

    # 以下是自定义应用
    'one_app.apps.One_appConfig',  # 完整形式
    'one_app',  # 简写形式
]
```

- 修改应用名

```shell
# 涉及要改动的地方
# 项目层
settings，注册应用名
urls.py
# 应用层
apps.py
urls.py
模版命名空间，form表单action
所有的迁移文件，alert表名
```

## 配置拆分

拆分后，如果要在非默认环境下启动和数据迁移等涉及数据库相关的命令记得加`--settings=config_path`

![20210826183930](http://image.zuoright.com/20210826183930.png)

> 设置`manage.py`默认使用`set_dev`配置：`os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'onestep.settings.set_dev')`
>
> 设置`wsgi.py`默认使用`set_dev`配置：`os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'onestep.settings.set_prod')`

- set_base

```python
import os
from pathlib import Path


# 基础配置
TIME_ZONE = 'UTC'  # 时区
LANGUAGE_CODE = 'en-us'  # 语言


# 静态文件路径配置
STATIC_URL = '/static/'  # 单独应用
STATICFILES_DIRS = [  # 公共
    os.path.join(BASE_DIR, "statics"),
]
```

- set_dev

```python
from .set_base import *

DEBUG=True

DATABASES = {}  # 开发环境数据库
```

- set_prod

```python
from .set_base import *

DEBUG=False
ALLOWED_HOSTS = ["*"]  # DEBUG等于False时必需配置

DATABASES = {}  # 生产环境数据库
```

## 配置数据库

### SQLite

SQLite是一种嵌入式数据库，它的数据库就是一个文件，体积很小，通常被集成在各种程序中，比如Python就内置了SQLite3，所以无需额外安装相关驱动

```python
# mysite/settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',  # 数据库引擎
        'NAME': BASE_DIR / 'db.sqlite3',  # sqlite的绝对路径
    }
}
```

### MySQL

> 参考：<https://docs.djangoproject.com/zh-hans/4.0/ref/databases/#mysql-notes>

- 创建数据库：`CREATE DATABASE <db_name>;`
- 配置

```python
# mysite/settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',  # 数据库引擎
        'NAME': 'dbname',  # 数据库名称
        'HOST': '127.0.0.1',  # host
        'PORT': 3306,  # 端口 
        'USER': 'root',  # 用户名
        'PASSWORD': 'passwd',  # 密码

        # 连接到数据库时要使用的额外参数
        # 'OPTIONS': {
        #     'read_default_file': '/path/to/my.cnf',  # 直接读取mysql的配置文件，此时会覆盖上面的配置
        #     'init_command': "SET sql_mode='STRICT_TRANS_TABLES'",  # 解决迁移时的一个警告
        #     'charset': 'utf8mb4',
        #     "isolation_level": "repeatable read",  # 隔离级别设置为可重复读取，为了迁移数据适配数据库默认Binlog=STATEMENT时报错
        # }
    }
}
```

- 安装 MySQLdb 驱动

```shell
# 如果设置为：'ENGINE': 'django.db.backends.mysql'
# 需要安装 mysqlclient 驱动，Django推荐
brew install mysql-client
pip install mysqlclient

# 如果设置为：'ENGINE': 'mysql.connector.django'
# 需要安装MySQL官方提供的驱动器：mysql-connector
pip install mysql-connector

# 如果想换成 pymysql 驱动
pip install pymysql
# 需要自己在 `mysite/__init__.py` 中引入并声明，貌似Django2.2以后会报错
"""
import pymysql
pymysql.install_as_MySQLdb()
"""
```

## 测试

为避免产生冗余数据，Django测试时会创建独立的临时数据库，测试完成后自动删除，因此会拖慢测试时间

如果不想新建数据库有两种处理方式

- 如果测试用例不涉及数据库

```python
# 使用SimpleTestCase替代TestCase
from django.test import SimpleTestCase
```

- 使用数据库但测试时复用同一个数据库

```shell
# 运行测试时添加--keepdb参数保留临时数据库（首次运行还是会创建的）
python manage.py test --keepdb
```

## 部署

> 使用 WSGI 或 ASGI 部署 Django 可以参考 CGI 部分的内容，或者[官方文档](https://docs.djangoproject.com/zh-hans/4.0/howto/deployment/)

### 内置Server启动

Django与Flask等框架都会内置一个简易的WSGI服务器，性能不高，主要用于开发阶段调试

> 参考：<https://docs.djangoproject.com/zh-hans/4.0/ref/django-admin/#runserver>

```shell
# 先切换到项目容器内
cd mysite

# 运行命令行工具manage.py启动项目，默认8000端口
# 首页地址：127.0.0.1:8000
# 根据默认配置启动
python manage.py runserver
# 可以更改端口
python manage.py runserver 8888
# 可以指定IP，比如监听所有服务器公开IP，0是0.0.0.0的缩写
python manage.py runserver 0:8000

# 指定配置文件启动
python manage.py runserver --settings=demo.settings.set_prod
```

- 前端：<http://127.0.0.1:8000/>
- 后台：<http://127.0.0.1:8000/admin>

### 检查部署

> 参考：<https://docs.djangoproject.com/zh-hans/4.0/howto/deployment/checklist/>

`python manage.py check --deploy`

### 解决非常规域名解析无效问题

报错信息：`The domain name provided is not valid according to RFC 1034/1035`

![20220705105539](http://image.zuoright.com/20220705105539.png)

大概原因是Django主机验证的正则表达式与我们的域名不匹配（比如域名包含了下划线），返回了空，所以需要我们覆盖这个正则

> 参考: <https://notabela.hashnode.dev/how-to-solve-the-domain-name-provided-is-not-valid-according-to-rfc-10341035-in-django-during-development>

```python
# manage.py

from django.utils.regex_helper import _lazy_re_compile
import django.http.request

django.http.request.host_validation_re = _lazy_re_compile(r"^([a-z0-9._.-]+|\[[a-f0-9]*:[a-f0-9:]+\])(:\d+)?$")
```

## 性能监控

Silk 是一个用于 Django Web 框架的性能分析工具和调试工具。它允许你监视和记录 Django 应用程序的性能数据，以帮助识别性能瓶颈、慢查询和其他性能问题。
