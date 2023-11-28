# 配置及部署

[官方文档](https://docs.djangoproject.com/zh-hans){ .md-button .md-button--primary }

Django 采用了 MVT 的框架模式，即：Model(模型)，View(视图)，Template(模版)

用户通过前端 Template 与表单交互，填写数据后提交表单，Views 通过 `HttpRequest` 对象接收用户输入的数据。通常，这些数据可以通过 `request.POST` 或 `request.GET` 访问，然后通过表单系统（`forms.Form` 或 `forms.ModelForm`）对表单数据进行验证和清理，以确保数据的安全性和有效性，一旦验证通过，可以使用这些数据执行相应的操作，如更新数据库、执行业务逻辑等，根据操作的结果，视图将创建一个 `HttpResponse` 对象来返回响应。这可能是一个重定向、渲染一个新的页面，或者仅仅是一个确认消息。

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

应用通常与 `manage.py` 所在路径同级，这样就可以作为顶级模块导入：`python manage.py startapp <app_name>`

当然也可以存放在任意路径：`django-admin startapp <app_name>`

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

USE_L10N = True  # 启用本地化，将应用翻译和调整到特定国家或地区
TIME_ZONE = 'UTC'  # 时区

USE_I18N = True  # 启用国际化，适应不同的语言和地区而无需进行工程上的更改
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

示例1

```python
# models.py
from django.db import models

class BlogPost(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    published = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)

    def is_published(self):
        return self.published


# tests.py
from django.test import TestCase
from .models import BlogPost
"""
为避免产生冗余数据，Django测试时会创建独立的临时数据库，测试完成后自动删除，因此会拖慢测试时间
可以使用 SimpleTestCase 替代 TestCase：from django.test import SimpleTestCase
"""
class BlogPostModelTest(TestCase):
    def test_is_published(self):
        # 创建一个博客帖子实例
        blog_post = BlogPost(title="Test Post", content="Test Content", published=True)

        # 测试 is_published 方法
        self.assertTrue(blog_post.is_published())
```

示例2

```python
from django.test import TestCase
from .models import MyModel

class MyModelTest(TestCase):
    def test_name_field(self):
        # 创建一个 MyModel 实例
        my_model_instance = MyModel(name="Test Name")
        my_model_instance.save()

        # 从数据库中检索刚创建的实例
        retrieved_instance = MyModel.objects.get(id=my_model_instance.id)

        # 断言检查 name 字段是否按预期工作
        self.assertEqual(retrieved_instance.name, "Test Name")
```

示例3

```python
from django.test import TestCase, Client
"""
Django提供了一个测试客户端，用于模拟发送 HTTP 请求到你的视图并接收响应
"""
class MyViewTest(TestCase):
    def setUp(self):
        self.client = Client()

    def test_my_view(self):
        # 发送 GET 请求到视图
        response = self.client.get('/my-view-url/')

        # 检查 HTTP 响应状态码
        self.assertEqual(response.status_code, 200)
```

运行

```shell
python manage.py test
python manage.py test app_name
python manage.py test app_name.tests.MyTestClass
python manage.py test app_name.tests.MyTestClass.test_my_method

# 执行完测试不销毁临时数据库，而是保留，下次测试复用，可以提高效率
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

# 运行命令行工具manage.py启动项目
# 根据默认配置启动：127.0.0.1:8000
python manage.py runserver

# 指定端口
python manage.py runserver 8888

# 指定IP，比如监听所有服务器公开IP，0是0.0.0.0的缩写
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

## 性能相关

### 优化

- 查询优化：使用 `select_related` 和 `prefetch_related` 来减少数据库查询次数。
- 使用缓存：利用 Django 的缓存框架来缓存视图、模板和数据。
- 静态文件优化：使用文件压缩和合并工具来减少静态文件的加载时间，或者托管到CDN。
- 避免过度使用 ORM：对于非常复杂或性能敏感的操作，考虑使用原生 SQL

### 监控

Silk 是一个用于 Django Web 框架的性能分析工具和调试工具。它允许你监视和记录 Django 应用程序的性能数据，以帮助识别性能瓶颈、慢查询和其他性能问题。

## 扩展

- django-rest-framework: 一个强大的工具集，用于构建 RESTful API。
- django-allauth: 提供了全面的账户管理功能，支持社交账户认证。
- django-celery: 集成了 Celery，一个异步任务队列/基于消息传递的分布式任务执行系统。
- django-cors-headers: 用于处理跨源资源共享（CORS）问题，特别是在开发 API 时非常有用。
- django-debug-toolbar: 一款强大的调试工具，提供了对 Django 项目的详细内部信息。
