# Django开发

学习Django直接看官方文档就可以了，[Django的官方文档](https://docs.djangoproject.com/zh-hans/3.2/contents/)是我见过最好最全的技术文档。

## 常用命令

- 创建项目：`django-admin startproject mysite`
- 创建应用：`python manage.py startapp one_app`
- 启动服务器：`python manage.py runserver`
- 生成迁移文件：`python manage.py makemigrations one_app`
- 数据迁移：`python manage.py migrate`
- 创建后台管理员：`python manage.py createsuperuser`
- 进入交互式命令行：`python manage.py shell`

## 项目

- 创建项目：`django-admin startproject mysite`
- 项目结构：

```shell
# 项目容器，可重命名
mysite/
    # 命令行工具
    manage.py
    # 项目
    mysite/
        __init__.py
        # 配置文件
        settings.py
        # 路由声明，网站目录
        urls.py
        # 兼容asgi的服务器入口
        asgi.py
        # 兼容wsgi的服务器入口
        wsgi.py
```

- 启动项目

```shell
# 先切换到项目容器内
cd mysite
# 运行命令行工具manage.py启动项目，默认8000端口
# 首页地址：127.0.0.1:8000
python manage.py runserver
# 可以更改端口
python manage.py runserver 8888
# 可以指定IP，比如监听所有服务器公开IP，0是0.0.0.0的缩写
python manage.py runserver 0:8000
```

## 配置

### 基本配置

```python
# mysite/settings.py
TIME_ZONE = 'UTC'  # 时区
LANGUAGE_CODE = 'en-us'  # 语言
```

### 数据库配置

- SQLite（默认，Python内置）

数据库会在需要的时候自动创建

```python
# mysite/settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',  # 数据库引擎
        'NAME': BASE_DIR / 'db.sqlite3',  # sqlite的绝对路径
    }
}
```

- 其它数据库

除了以下配置还需要数据库驱动，并且先创建数据库，以及必要的权限

安装驱动：`pip install pymysql`

```python
# 配置
# mysite/settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',  # 数据库引擎
        'NAME': 'mydatabase',  # 数据库名称
        'HOST': '127.0.0.1',  # host
        'PORT': 3306,  # 端口 
        'USER': 'root',  # 用户名
        'PASSWORD': '123456',  # 密码
    }
}
```

声明

```python
# __init__.py
import pymysql

pymysql.install_as_MySQLdb()  # 告诉django用pymysql代替mysqldb连接数据库
```

## 应用

一个项目可以包含多个应用，一个应用可以被多个项目使用

应用可以存放在任意路径，不过通常与manage.py同级，这样就可以作为顶级模块导入

- 创建应用：`python manage.py startapp one_app`
- 应用结构：

```shell
one_app/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
    # 以下文件自己创建
    urls.py  # URLconf路由配置文件（也可以是其他任何路径）
    templates/  # 模版容器
        one_app/  # 命名空间，由于DjangoTemplates会在每个INSTALL_APPS路径下依次寻找templates子目录，如果多个APP之间模板名称一样，只会匹配到第一个，容易出错。
            index.html  # 模版
```

## 应用模型 models.py

### 应用声明

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

### 为声明的应用创建表（table）

`python manage.py migrate`

查看创建了哪些表：`.schema`(SQLite)，`SHOW TABLES;`(MySQL)

### 创建管理员账号

`python manage.py createsuperuser`

然后启动服务器访问：127.0.0.1:8000/admin 即可访问后台

### 定义模型

```python
from django.db import models

# 每个类就是一个模型(sheet)，继承自models.Model
class Question(models.Model):
    # 每个属性代表一个字段(field)
    # 格式：字段名 = models.类型(参数, "备注名")
    # 主键id字段不需要定义，会被自动添加
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

    # 在执行Question.objects.all()时默认执行这个方法
    def __str__(self):
        return self.question_text  # 可以返回一个能标识这个模型的字段

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)  # 外键
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

[字段类型及参数](https://docs.djangoproject.com/zh-hans/3.1/ref/models/fields/#)

字段名：不能用Python保留字，不能连续用多个下划线且不能以下划线结尾

字段备注名：默认使用字段的属性名作为备注名

常见字段类型：

- models.AutoField(primary_key=True)  模型会自动设置id字段为自增主键
- models.ForeignKey(想要关联的模型类名, on_delete=models.CASCADE)  外键（多对一关联，还可以自关联(递归）)
- models.IntegerField()  整数
- models.CharField(max_length=num)  字符串
- models.BooleanField()  布尔值
- models.DateField(auto_now_add/auto_now=True)  日期(第一次创建时设置为现在时间/每次保存时(更新时不会)设置为现在时间)
- models.DateTimeField(auto_now_add/auto_now=True)  日期和时间
- models.JSONField(encoder=None, decoder=None)  v3.1版本新加Json类型
- models.URLField(max_length=num)  URL
- models.UUIDField()  唯一标识符

字段通用参数：

- `blank=True` 字段为空时就是空
- `null=True` 字段为空时设置为NUL
- `choices=[("S", "Small"), (“B”, “Big”)]` 枚举值（二元组的第一个值存在数据库中，第二个值用于显示）
- `default=xxx` 默认值，一般为None
- `help_text=xxx` 注释文本
- `primary_key=True` 设置为主键，一般不需要手动设置主键，默认会添加一列id字段为主键
- `unique=True` 字段的值唯一，即不能重复

```python
# 数据库迁移
# 生成数据迁移文件(SQL脚本)(生成到migrations/路径下)
python manage.py makemigrations
# 为每个应用自动生成需要的数据库表，具体创建什么取决于数据库迁移文件
python manage.py migrate
# 创建后台管理员账号
python manage.py createsuperuser
# 根据提示输入用户名邮箱和密码（admin，admin@zuoright.com，123456）
# 访问后台：127.0.0.1:8000/admin
```

### 数据迁移（创建/修改sheet）

- step1：生成迁移数据（SQL语句）

```shell
python manage.py makemigrations one_app
```

迁移数据存储在：`one_app/migrations/0001_initial.py`中

查看生成了哪些SQL语句：`python manage.py sqlmigrate one_app 0001`

- step2：执行迁移（执行SQL语句）

```shell
python manage.py migrate
```

## 数据库API

```python
from one_app.models import Blog

# SELECT语句
Q = Blog.objects  #  Managerd对象，表级操作
Q.all()  # 等同于Q，返回一个包含所有记录的QuerySet对象集合
Q.order_by("xxx")  # 按某字段排序

# WHERE子句
Q.get(pk=1)  # 如果查询结果只有一条记录可以用get，但如果查不到或查到多条会报错
Q.filter(**kwargs)  # 过滤满足条件的记录，WHERE xxx AND xxx
Q.exclude(**kwargs)  # 排除满足条件的记录，WHERE NOT(xxx AND xxx)
Q.exclude().exclude()  # WHERE NOT xxx AND NOT xxx

# LIMIT和OFFSET子句，切片，不支持负数索引，可以用步长2
Q.all()[:5]  # LIMIT 5 返回前5个对象
Q.all()[5:10]  # OFFSET 5 LIMIT 5 返回第6～10个对象

r = Blog(Field1="x1", Field2="x2")  # 实例初始化，行级操作
r.Field1 = "x01"  # 修改字段值

r.save()  # 保存
```

字段查询

```python
# 完全匹配 =
Q.get(name__exact="abc")  # WHERE name = "abc";
Q.get(name="abc")  # 同上

# 比较
__gt  # 大于
__gte  # 大于等于
__lt   # 小于
__lte  # 小于等于

# LIKE 区分大小写
Q.get(name__contains="abc")  # WHERE name LIKE '%abc%';
Q.filter(name__startswith="abc")  # WHERE name LIKE 'abc%';
Q.filter(name__endswith="abc")  # WHERE name LIKE '%abc';

# ILIKE 不区分大小写
Q.get(name__iexact="abc")  # WHERE name ILIKE 'abc';
Q.get(name__icontains="xxx")  # WHERE name ILIKE '%abc%';
Q.get(name__istartswith="abc")  # WHERE name ILIKE 'abc%';
Q.get(name__iendswith="abc")  # WHERE name ILIKE '%abc';

# IN
Q.filter(id__in=[1, 3, 4])  # WHERE id IN (1, 3, 4);

# BETWEEN...AND...
Q.filter(date__range=(start_date, end_date))  # WHERE date BETWEEN 'start_date' and 'end_date';
```

## 管理后台 admin.py

```python
# one_app/admin.py
from django.contrib import admin
from .models import Question

# 把应用注册到管理后台
admin.site.register(Question)
```

然后就可以对记录进行操作，增删改查

## 视图函数 views.py

### HttpRequest

```python
from .models import Question  # 引入模型
from django.shortcuts import render  # 引入快捷函数


def index(request):  # request=<HttpRequest object>
    # 通过request.POST字典对象获取表单提交的数据
    # 取不到值会报KeyError，取到的值永远是字符串
    try:
        xxx = request.POST["key_name"]
    except (KeyError, Choice.DoesNotExist):
        pass
    else:
        pass

    return render(request, 'one_app/index.html', context)
```

### HttpResponse

- 方式1，HttpResponse返回文本

```python
from django.http import HttpResponse

def index(request):
    ...
    output = xxx
    return HttpResponse(output)
```

- 方式2，HttpResponse返回模板

```python
from django.http import HttpResponse
from django.template import loader

def index(request):
    a = xxx
    context = {"key": a}  # 上下文

    template = loader.get_template('one_app/index.html')  # 载入模板文件
    return HttpResponse(template.render(context, request))  # 传递上下文
```

- 方式3，HttpResponse用快捷函数形式返回模板（推荐）

```python
from django.shortcuts import render, get_object_or_404

def index(request):
    a = xxx
    context = {"key": a}

    # 格式：render(request对象，模板名称，上下文字典)
    return render(request, 'one_app/index.html', context)
```

### 重定向

```python
from django.http import HttpResponseRedirect

# 构造函数：HttpResponseRedirect(重定向后的URL)
# 视图函数中避免URL硬编码：reverse(视图名字，args=(视图所需参数的元组,))
return HttpResponseRedirect(reverse('one_app:results', args=(question.id,)))  # /one_app/3/results/
```

### 404

- 方式1

```python
from django.http import Http404
from django.shortcuts import render

from .models import Question

def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'one_app/detail.html', {'question': question})
```

- 方式2，快捷函数方式返回（推荐）

```python
from django.shortcuts import get_object_or_404, render

from .models import Question

def detail(request, question_id):
    # 格式：get_object_or_404(模型对象，任意数量的关键字参数)
    # 如果关键字参数查不到结果则返回404
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'one_app/detail.html', {'question': question})
```

### 路由配置 urls.py

- 配置项目的路由，根路由匹配应用

```python
# mysite/urls.py
from django.urls import path, include
from django.contrib import admin

urlpatterns = [
    # 必选参数view：如果要引用其它的URLconfs，应该总是使用inlcude()这种形式
    path('one_app/', include('one_app.urls')),
    # admin.site.urls是唯一的例外
    path('admin/', admin.site.urls),
]
```

- 配置应用的路由，应用匹配页面

```python
# one_app/urls.py
from django.urls import path
from . import views

# 添加命名空间，避免多个应用路由名称相同混淆
app_name = "one_app"

urlpatterns = [
    # 格式：path(route, view, kwargs, name)
    # 必选route：路由正则
    # 必选view：调用视图函数，传入HttpRequest对象
    # 可选kwargs：字典的形式给视图函数传递任意个关键字
    # 可选name：给路由起名字，在项目任意位置引用
    path('', views.index, name='index'),
]
```

## 模板

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>index</title>
    <link rel="stylesheet" type="text/css" href="{百分号 static 'xxx.css' 百分号}"> 
</head>
<body>

</body>
</html>
```

去除硬编码URL

```html
<a href="/one_app/{{ question.id }}/">xxx</a>
<!-- 去除后 -->
<a href="{百分号 url 'one_app:detail' question.id 百分号}">xxx</a>
```

所有针对内部 URL 的 POST 表单都应该使用 {百分号 csrf_token 百分号} 模板标签，防止跨站点请求伪造。

```html
<form action="{百分号 url 'one_app:vote' question.id 百分号}" method="post">
{百分号 csrf_token 百分号}
</form>
```

加载样式

```html
<!-- 生成静态文件的绝对路径 -->
{百分号 load static 百分号}
<link rel="stylesheet" type="text/css" href="{百分号 static 'one_app/style.css' 百分号}">
```

加载图片

```css
/* 图片存放在one_app/static/one_app/images/xxx.gif */
body {
    background: white url("images/xxx.gif") no-repeat;
}
```

## 通用视图

```python
# one_app/views.py
from django.views import generic
from .models import Question

# 通用视图ListView会显示一个特定类型对象的列表页面
class IndexView(generic.ListView):
    # 通过model属性告知通用视图作用于哪个模型
    model = Question
    # 通过template_name属性告知通用视图使用哪个模版
    # 如果不指定，ListView默认使用<app name>/<model name>_list.html做模版
    template_name = 'one_app/index.html'
    # 通过context_object_name属性告知通用视图上下文变量
    # 如果不指定，默认为question_list
    context_object_name = 'latest_question_list'

# 通用视图DetailView会显示一个特定类型对象的详细信息页面
class ResultsView(generic.DetailView):  # 不再需要传递request和question_id
    model = Question
    # 如果不指定template_name，DetailView默认使用<app name>/<model name>_detail.html做模版
    template_name = 'one_app/results.html'
    # 如果不指定context_object_name，默认为question
```

```python
# one_app/urls.py
from django.urls import path
from . import views

app_name = 'one_app'
urlpatterns = [
    path('', views.IndexView.as_view(), name='index'),
    # DetailView会捕获名为"pk"的主键值，所以路由参数的变量名要改为"pk"
    # 视图参数也要改一下
    path('<int:pk>/results/', views.ResultsView.as_view(), name='results'),
]
```
