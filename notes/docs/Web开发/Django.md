# Django

[Django的官方文档(有中文版)](https://docs.djangoproject.com/zh-hans){ .md-button .md-button--primary }

Django采用了MTV的框架模式，即：Model(模型)，Template(模版)，View(视图)

## 项目

```shell
mkdir mysite
cd mysite
python -m venv env  # Django 4.0依赖Python 3.8+
pip install django

django-admin startproject mysite .  # 初始化项目，注意结尾的点
```

```text
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

- `mysite/settings.py`

```python
# 声明应用
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

- 启动服务：`python manage.py runserver`
- 前端页面：<http://127.0.0.1:8000/>
- admin后台：<http://127.0.0.1:8000/admin>

## 应用

一个项目可以包含多个应用，一个应用可以被多个项目使用，应用可以存放在任意路径，不过通常与manage.py同级，这样就可以作为顶级模块导入

创建应用：`python manage.py startapp one_app`

```text
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

- 修改应用名涉及要改动的地方

```text
项目层
    settings，注册应用名
    urls.py

应用层
    apps.py
    urls.py
    模版命名空间，form表单action
    所有的迁移文件，alert表名
```

### 定义模型：`one_app/models.py`

> 参考：<https://docs.djangoproject.com/zh-hans/4.0/topics/db/models/#>

```python
from django.db import models
from django.contrib.auth.models import User

# Create your models here.
# 每个类就是一个模型(sheet)，继承自models.Model
class Cases(models.Model):
     """
    每个属性代表一个字段(field)，主键id字段不需要定义，会被自动添加
    格式：字段名 = models.类型(参数, "备注名")
        字段名不能用Python保留字，不能连续用多个下划线且不能以下划线结尾
        备注名默认为字段名，可自定义
    """

    """
    枚举值变量建议定义在类内部
    如果是一个易变的枚举列表，建议使用外键
    """
    ORDER_TYPE_CHOICES = [
        ("MARKET", "MARKET"),
        ("LIMIT", "LIMIT"),
        ("TWAP_ALGO", "TWAP_ALGO")
    ]

    def payload_default():
        return {"delta": 0.01}

    exchange = models.ForeignKey(Exchange, on_delete=models.CASCADE)  # 外键
    subject = models.ForeignKey(Subject, on_delete=models.CASCADE)
    currency = models.ForeignKey(Currency, on_delete=models.CASCADE)
    order_type = models.CharField(max_length=20, choices=ORDER_TYPE_CHOICES, default="LIMIT")
    side = models.CharField(max_length=5, default="1")
    payload = models.JSONField("Payload", default=payload_default)
    priority = models.CharField(
        max_length=5,  # CharField类型必须带max_length属性
        choices=[("p0","P0"), ("p1","P1"), ("p2","P2"), ("p3","P3")],  # 使用枚举值
        default="p0", 
        help_text="P0一般为主流币种正向用例, P1为小币种用例, P2/P3通常为边界值或异常场景用例"
    )
    is_test = models.IntegerField(choices=[(1,"Yes"), (0,"No")], default=1)
    expect_succeed = models.IntegerField(choices=[(1,"True"), (0,"False")], default=1)
    note = models.CharField(max_length=50, blank=True)

    creator = models.ForeignKey(User, verbose_name="创建人", null=True, on_delete=models.SET_NULL)
    created_date = models.DateTimeField(verbose_name="创建日期", auto_now_add=True)
    modified_date = models.DateTimeField(verbose_name="修改日期", auto_now=True)

    class Meta:
        verbose_name = 'Exchange'


class Exchange(models.Model):
    exchange = models.CharField(max_length=20)

    class Meta:
        # 在admin后台显示的导航文案
        verbose_name = 'Exchange'
        verbose_name_plural = '交易所'  # 复数

    def __str__(self):
        # 默认返回值，比如作为外键时，在admin后台显示为变量值，而不是关联id
        return self.exchange
```

- 常见字段类型

```python
models.AutoField(primary_key=True)  # 模型会自动设置id字段为自增主键
models.IntegerField()  # 整数
models.CharField(max_length=num)  # 字符串，必须设置max_length
models.BooleanField()  # 布尔值
models.DateField(auto_now_add/auto_now=True)  # 日期(第一次创建时设置为现在时间/每次保存时(更新时不会)设置为现在时间)
models.DateTimeField(auto_now_add/auto_now=True)  # 日期和时间
models.JSONField(encoder=None, decoder=None)  # Json类型(v3.1版本新加，另外mysql5.7.8以下版本不支持Json类型）
models.URLField(max_length=num)  # URL
models.UUIDField()  # 唯一标识符

models.ForeignKey(想要关联的模型类名, on_delete=models.CASCADE)  # 外键（多对一关联，还可以自关联(递归）)
```

- 字段通用参数

```python
primary_key=True  # 设置为主键，一般不需要手动设置主键，默认会添加一列id字段为主键
unique=True  # 字段的值唯一，即不能重复

blank=True  # 字段为空时就是空
null=True  # 字段为空时设置为NULL
default=xxx  # 默认值，一般为None

help_text=xxx  # 注释文本

choices=[("S", "Small"), (“B”, “Big”)]  # 枚举值，[("存入数据库中的值", "仅用于显示")]
```

- Meta 属性

> 参考：<https://docs.djangoproject.com/zh-hans/4.0/ref/models/options/>

### 模型继承

> 参考：<https://docs.djangoproject.com/zh-hans/4.0/topics/db/models/#model-inheritance>

三种方式

- 抽象基类

基类不会创建表

通过在基类中设置Meta属性`abstract = True`实现

```python
from django.db import models

class CommonInfo(models.Model):
    name = models.CharField(max_length=100)
    age = models.PositiveIntegerField()

    class Meta:
        abstract = True
        """
        CommonInfo将变为一个抽象基类，不会生成实际的数据表
        抽象基类中的字段可以被子类继承、覆盖、也可以等于None软删除
        子类也会继承Meta类，但会自动设置abstract=False，所以子类不会因为继承了基类而变成抽象基类
        """

class Student(CommonInfo):
    home_group = models.CharField(max_length=5)
    age = None  # 子类中不用这个字段
```

- 多表继承

子类继承自非抽象基类，子类不能覆盖基类的字段，子类与父类都会创建表

无需额外设置

```python
from django.db import models

class Place(models.Model):
    name = models.CharField(max_length=50)
    address = models.CharField(max_length=80)

class Restaurant(Place):
    serves_hot_dogs = models.BooleanField(default=False)
    serves_pizza = models.BooleanField(default=False)
```

- 代理模型

子类与父类字段完全一致，仅仅改变行为，与基类共用一张表

通过在子类中设置Meta属性`proxy = True`实现

```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)

"""
至少要继承一个非抽象基类，可以继承自多个抽象父类，以及多个代理模型（但必须拥有同一个非抽象基类）
"""
class MyPerson(Person):
    class Meta:
        proxy = True

    def do_something(self):
        # ...
        pass
```

## 数据库配置

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

- 创建数据库：`create database if not exists dbname default character set utf8 collate utf8_general_ci;`
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
            'PASSWORD': '12345678',  # 密码

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

- 安装MySQLdb驱动

    ```shell
    # Django推荐，使用'ENGINE': 'django.db.backends.mysql'，默认使用mysqlclient驱动库
    brew install mysql-client
    pip install mysqlclient

    # MySQL官方提供的驱动器，使用'ENGINE': 'mysql.connector.django'，默认使用mysql-connector驱动库
    pip install mysql-connector

    # 如果想换成pymysql驱动，需要自己在mysite/__init__.py中引入并声明，貌似Django2.2以后会报错
    pip install pymysql
    """
    import pymysql
    pymysql.install_as_MySQLdb()
    """
    ```

## 数据迁移

```shell
# 查看迁移状态，[x]表示已迁移
# 也可以用来检查数据库配置是否正确，有问题会报错
python manage.py showmigrations
"""
admin
 [x] 0001_initial
 [x] 0002_logentry_remove_auto_add
 [x] 0003_logentry_add_action_flag_choices
auth
 [ ] 0001_initial
 [ ] 0002_alter_permission_name_max_length
 [ ] 0003_alter_user_email_max_length
 [ ] 0004_alter_user_username_opts
 [ ] 0005_alter_user_last_login_null
 [ ] 0006_require_contenttypes_0002
 [ ] 0007_alter_validators_add_error_messages
 [ ] 0008_alter_user_username_max_length
 [ ] 0009_alter_user_last_name_max_length
 [ ] 0010_alter_group_name_max_length
 [ ] 0011_update_proxy_permissions
 [ ] 0012_alter_user_first_name_max_length
contenttypes
 [ ] 0001_initial
 [ ] 0002_remove_content_type_name
one_app
 (no migrations)
sessions
 [ ] 0001_initial
"""

# 生成迁移数据（生成SQL脚本）
python manage.py makemigrations one_app
"""
迁移数据存储在：one_app/migrations/0001_initial.py 中
查看生成了哪些SQL语句：python manage.py sqlmigrate one_app 0001
"""

# 执行迁移（执行SQL语句）
python manage.py migrate
"""
查看创建了哪些表：
    SQLite: .schema
    MySQL: SHOW TABLES;
"""
```

### 重置迁移文件

```shell
# 查看当前迁移文件记录及状态
python manage.py showmigrations
# 将某应用的迁移文件重置为未提交状态
python manage.py migrate --fake 应用名 zero
# 然后手动删除应用下的migrations文件（内置应用存放在env/lib/python/site-packages/django/contrib下）
# 重新生成migrations文件
python manage.py makemigrations
# 重新在django_migrations表中加入记录
python manage.py migrate 应用名 --fake-initial
```

### 压缩迁移文件

```shell
# 假设要压缩的应用为myapp，现在有5个迁移文件，最新的为0005_xxx
# 生成压缩后的迁移文件
python manage.py squashmigrations 0005
# 把新的迁移文件提交下
python manage.py makemigrations
# 然后删除旧的迁移文件，把依赖旧迁移文件的地方(主要是其他应用中)都替换为新的文件名
# 删除压缩迁移的 Migration 类的 replaces 属性
# 最后执行下迁移命令不报错则说明压缩成功
python manage.py migrate
```

我曾经闲的蛋疼为了解决另一个坑压缩过`contenttypes`应用的迁移文件，使得`django_migrations`表中第一行记录不是`contenttypes.0001_initial`，导致在更换Python运行环境后执行`python manage.py xxx`相关的命令时，出现下面这样一个报错：

> `django.db.migrations.exceptions.InconsistentMigrationHistory: Migration admin.0001_initial is applied before its dependency contenttypes.0001_initial on database 'default'`

当时查了个遍也没有得到一个满意的答案，大多数解决方式是建议必须重置数据库，但我觉得并不至于要这样做，睡了一觉之后第二天下班之后，决定静下心来自己解决掉它，最终只需要在`django_migrations`表中第一行补上`contenttypes.0001_initial`这条记录就可以了，就是这么简单。

!!! 总结
    不要随便压缩并且重命名Django自带应用的数据迁移文件（内心OS：但我记得之前确实是为了改一个Django本身的问题，而那个问题是因为迁移文件的命名是数字开头，导致依赖引用的时候报了语法错误，这是不符合Python文件命名规范的，Shit）

### 导入导出数据

```shell
# 导出数据，不指定app_name默认导出所有表
python manage.py dumpdata [app_name] > path/data.json
# 导入数据，不需要指定app_name
python manage.py loaddata path/data.json
```

## 后台管理

> 参考：<https://docs.djangoproject.com/zh-hans/4.0/ref/contrib/admin/>

### 注册应用 `one_app/admin.py`

```python
from django.contrib import admin
from .models import Demo


admin.site.site_header = "QAdmin"  # 顶部显示名称
admin.site.site_title = 'QAdmin'  # title名称


# Register your models here.
@admin.register(Demo)  # 也可以这样：admin.site.register(Demo, DemoAdmin)
class DemoAdmin(admin.ModelAdmin):
    # 详情页
    inlines = [StepInline]  # 改为行内布局，默认StackedInline堆叠布局
    fields = (('is_test', 'expect_succeed'), 'note')  # 显示的字段，同一元组的字段显示在同一行
    exclude = ('creator','created_date','modified_date')  # 不显示的字段

    # 列表页
    list_display = ("name", 'cmd', "group", 'creator', 'created_date', 'modified_date')  # 在列表中显示的字段
    list_display_links = ('subject', 'currency')  # 可点击跳转到详情页的字段，默认为list_display第一个字段，为None将没有任何链接
    list_editable = ("group",)  # 列表中可编辑的字段，list_display_links中的字段不允许设置为可编辑的
    list_filter = ("group", "creator")  # 列表中可筛选的字段，默认支持多个字段联合筛选，利用双下划线可以进行跨表关联
    search_fields = ("name", 'cmd')  # 列表中可被搜索的字段

    ordering = ('id',)  # 列表自动排序字段
    sortable_by = ("subject")  # 支持手动排序的字段，不限制则所有list_display字段都可以排序，空元组则禁用所有排序

    # 如果设置了创建者字段，加入此方法表示保存时自动添入当前User为创建者
    def save_model(self, request, obj, form, change):
        if obj.creator is None:
            obj.creator = request.user
        super().save_model(request, obj, form, change)

    """
    列表的action中默认只有删除数据
    我们可以添加自定义的action
    """
    # 指定action
    actions = ["demo_action"]
    # 定义action
    def demo_action(self):
        pass
    demo_action.short_description = "脚本显示名称"
    # 将action加入到admin下拉框中显示
    def get_action_choices(self, request):
        choices = super(DemoAdmin, self).get_action_choices(request)
        choices.pop(0)
        choices.reverse()
        return choices


# 可将多个模型注册到同一个类上
@admin.register(Exchange, Subject, Currency)
class HiddenModelAdmin(admin.ModelAdmin):
    # 隐藏模型，不在导航上显示索引，但仍然可以外键引用的时候编辑和新建
    def has_module_permission(self, request) -> bool:
        return False
```

### 创建账号

```python
# 创建后台管理员账号
python manage.py createsuperuser
# 根据提示输入用户名邮箱和密码
#   admin
#   admin@zuoright.com
#   密码不能少于8位，且不能全是数字：password
# 访问后台：127.0.0.1:8000/admin
```

```shell
# 进入交互式命令行
python manage.py shell

# 重置用户密码
from django.contrib.auth.models import User
user = User.objects.get(username='admin')
user.set_password('xxxx')
user.save()
```

## 修改应用在后台的显示名

> 参考：<https://docs.djangoproject.com/zh-hans/4.0/ref/applications/#for-application-authors>

```python
from django.apps import AppConfig


class OnestepConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'onestep'  # 应用名
    verbose_name = '一步'  # 显示名，不设置此字段时默认为应用名
```

## 数据库API

```python
# 可用于admin.py或views.py
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

- 字段查询

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

### 防止跨站点请求伪造

所有针对内部 URL 的 POST 表单都应该使用 {百分号 csrf_token 百分号} 模板标签，防止跨站点请求伪造。

```html
<form action="{百分号 url 'one_app:vote' question.id 百分号}" method="post">
{百分号 csrf_token 百分号}
</form>
```

### 去除硬编码URL

```html
<a href="/one_app/{{ question.id }}/">xxx</a>
<!-- 去除后 -->
<a href="{百分号 url 'one_app:detail' question.id 百分号}">xxx</a>
```

### 过滤器

过滤器的`|`符号左右最好不要加空格，否则被for循环时会报错

自定义过滤器

![20210621191818](http://image.zuoright.com/20210621191818.png)

### 静态文件

```html
<!-- 文件存放在one_app/static中 -->
{百分号 load static 百分号}
<link rel="stylesheet" type="text/css" href="{百分号 static 'css/style.css' 百分号}">

<img width="180" height="180" src="{百分号 static 'img/xxx.jpg' 百分号}"
```

## 配置拆分

拆分后，如果要在非默认环境下启动和数据迁移等涉及数据库相关的命令记得加`--settings=config_path`

![20210826183930](http://image.zuoright.com/20210826183930.png)

> 设置`manage.py`默认使用`set_dev`配置：`os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'onestep.settings.set_dev')`
>
> 设置`wsgi.py`默认使用`set_dev`配置：`os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'onestep.settings.set_prod')`

### set_base

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

### set_dev

```python
from .set_base import *

DEBUG=True

DATABASES = {}  # 开发环境数据库
```

### set_prod

```python
from .set_base import *

DEBUG=False
ALLOWED_HOSTS = ["*"]  # DEBUG等于False时必需配置

DATABASES = {}  # 生产环境数据库
```

此时Django不会再提供静态文件服务

- 方式1(适用于命令行启动)：`python manage.py runserver --insecure`
- 方式2 CDN

1. 配置文件中先把所有注册APP分散的静态资源都收集到统一位置：`STATIC_ROOT = os.path.join(BASE_DIR, 'static_cdn')`
2. 命令行执行收集命令：`python manage.py collectstatic`，将项目用到的所有静态文件集中存放在`static_cdn`路径下
3. 然后用Nginx提供静态资源服务

    ```ini
    location /static/ {
        alias /usr/share/nginx/mysite/static_cdn/;
    }
    ```

4. 重新加载：`service nginx reload`

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

> 使用 WSGI 或 ASGI 部署 Django 可以参考[CGI](cgi.md)，或者[官方文档](https://docs.djangoproject.com/zh-hans/4.0/howto/deployment/)

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
