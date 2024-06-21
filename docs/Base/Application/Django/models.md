# Models

## 定义模型

> 参考：<https://docs.djangoproject.com/zh-hans/4.2/topics/db/models/>

`models.py`

```python
from django.db import models  # 引入ORM
from django.contrib.auth.models import User  # 内置的用户模型

# 每个类就是一个表，继承自 models.Model
class Exchange(models.Model):
    """
    每个属性代表一个字段(field)，主键id字段不需要定义，会被自动添加
    格式：
        字段名 = models.类型(参数, "备注名")
        字段名不能用Python保留字，不能连续用多个下划线且不能以下划线结尾
        备注名默认为字段名，可自定义
    """
    exchange = models.CharField(max_length=20)

    class Meta:
        verbose_name = 'Exchange'  # 在admin后台显示的导航文案
        verbose_name_plural = '交易所'  # 复数

    def __str__(self):
        # 默认返回值，比如作为外键时，在admin后台显示为变量值，而不是关联id
        return self.exchange


class Cases(models.Model):
    """
    枚举值变量建议定义在类内部
    如果是一个易变的枚举列表，建议使用外键
    """
    ORDER_TYPE_CHOICES = [
        ("MARKET", "MARKET"),
        ("LIMIT", "LIMIT"),
        ("TWAP_ALGO", "TWAP_ALGO")
    ]

    # ForeignKey 外键
    exchange = models.ForeignKey(Exchange, on_delete=models.CASCADE)
    subject = models.ForeignKey(Subject, on_delete=models.CASCADE)
    currency = models.ForeignKey(Currency, on_delete=models.CASCADE)

    order_type = models.CharField(
        max_length=20,  # CharField必须带max_length属性
        choices=ORDER_TYPE_CHOICES,  # 枚举值
        default="LIMIT"  # 默认值
    )
    side = models.CharField(max_length=5, default="1")
    payload = models.JSONField(blank=True, default=dict)  # 默认值可以引用一个函数，比如dict表示空字典{}，默认值为{}时必须设置blank=True
    priority = models.CharField(
        max_length=5,
        choices=[("p0","P0"), ("p1","P1"), ("p2","P2"), ("p3","P3")],
        default="p0", 
        help_text="P0一般为主流币种正向用例, P1为小币种用例, P2/P3通常为边界值或异常场景用例"  # 注释
    )
    is_test = models.IntegerField(choices=[(1,"Yes"), (0,"No")], default=1)
    expect_succeed = models.IntegerField(choices=[(1,"True"), (0,"False")], default=1)
    note = models.CharField(max_length=50, blank=True)

    creator = models.ForeignKey(User, verbose_name="创建人", null=True, on_delete=models.SET_NULL)
    created_date = models.DateTimeField(verbose_name="创建日期", auto_now_add=True)
    modified_date = models.DateTimeField(verbose_name="修改日期", auto_now=True)

    class Meta:
        verbose_name = 'Exchange'
```

### 字段类型

```python
models.AutoField(primary_key=True)  # 模型会自动设置id字段为自增主键
models.IntegerField()  # 整数
models.CharField(max_length=num)  # 字符串，必须设置 max_length
models.BooleanField()  # 布尔值
models.DateField(auto_now_add/auto_now=True)  # 日期(第一次创建时设置为现在时间/每次保存时(更新时不会)设置为现在时间)
models.DateTimeField(auto_now_add/auto_now=True)  # 日期和时间
models.JSONField(encoder=None, decoder=None)  # Json类型(v3.1版本新加，另外mysql5.7.8以下版本不支持Json类型）
models.URLField(max_length=num)  # URL
models.UUIDField()  # 唯一标识符

models.ForeignKey(想要关联的模型类名, on_delete=models.CASCADE)  # 外键（多对一关联，还可以自关联(递归）)
```

### 字段选项

```python
primary_key=True  # 设置为主键，一般不需要手动设置主键，默认会添加一列id字段为主键
unique=True  # 字段的值唯一，即不能重复

blank=True  # 字段为空时就是空
null=True  # 字段为空时设置为NULL
default=xxx  # 默认值，一般为None

help_text=xxx  # 注释文本

choices=[("S", "Small"), (“B”, “Big”)]  # 枚举值，[("存入数据库中的值", "仅用于显示")]
```

## 模型继承

三种方式参考：<https://docs.djangoproject.com/zh-hans/4.0/topics/db/models/#model-inheritance>

Meta 属性参考：<https://docs.djangoproject.com/zh-hans/4.0/ref/models/options/>

### 抽象基类

基类不会创建表，之类会创建表

通过在基类中设置Meta属性 `abstract = True` 实现

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

### 代理模型

子类与父类字段完全一致，仅仅改变行为，与基类共用一张表

通过在子类中设置Meta属性 `proxy = True` 实现

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

### 多表继承

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

## 数据迁移

- 生成SQL语句
- 根据生成的SQL语句创建或更新数据表
- 版本跟踪管理

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
python manage.py makemigrations [one_app]
"""
迁移数据存储在：one_app/migrations/0001_initial.py 中
查看生成了哪些SQL语句：python manage.py sqlmigrate one_app 0001
"""

# 执行迁移（执行SQL语句）
python manage.py migrate

# 查看创建了哪些表
"""
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

## Fixture

<https://docs.djangoproject.com/en/4.2/topics/db/fixtures/>

### 生成固定数据

可以从已有的数据库导出数据

```shell
python manage.py dumpdata app_name.ModelName > filename.json
```

也可以手写一些 JSON，XML 或 YAML 格式的数据放入 APP 下的 `fixtures/` 路径

比如给 Person 模型提供一些数据

- json

```json
[
  {
    "model": "myapp.person",
    "pk": 1,
    "fields": {
      "first_name": "John",
      "last_name": "Lennon"
    }
  },
  {
    "model": "myapp.person",
    "pk": 2,
    "fields": {
      "first_name": "Paul",
      "last_name": "McCartney"
    }
  }
]
```

- yaml

需要安装 PyYAML

```yaml
- model: myapp.person
  pk: 1
  fields:
    first_name: John
    last_name: Lennon
- model: myapp.person
  pk: 2
  fields:
    first_name: Paul
    last_name: McCartney
```

### 使用固定数据

- 用于测试

```python
class MyTestCase(TestCase):
    fixtures = ["filename_a", "filename_b", "filename_c"]
```

- 用于初始化数据

```shell
python manage.py loaddata filename.json

# 或者
django-admin loaddata filename.json  # 加载Json格式的数据
django-admin loaddata filename  # 加载所有格式的数据

# 可同时加载多个fixture
django-admin loaddata filename_a filename_b filename_c
```

## 数据库API

> <https://docs.djangoproject.com/zh-hans/4.0/ref/models/querysets/#queryset-api-reference>

```python
# 可用于admin.py或views.py
from one_app.models import Blog

# SELECT语句
Q = Blog.objects  #  Managerd对象，表级操作
Q.all()  # 等同于Q，返回一个包含所有记录的QuerySet对象集合
Q.order_by("xxx")  # 按某字段排序

# WHERE子句
r = Q.get(pk=1)  # 如果查询结果只有一条记录可以用get，但如果查不到或查到多条会报错
r.xxx  # 会重新访问数据库
r = Q.select_related("xxx").get(pk=1)
r.xxx  # 不需要重新访问数据库，性能更高

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
__gt  # 大于 greater than
__gte  # 大于等于 greater than or equal to
__lt   # 小于 less than
__lte  # 小于等于 less than or equal to
__exact  # 精确等于（区分大小写）
__iexact  # 精确等于（不区分大小写）
__contains  # 包含（区分大小写）
__icontains  # 包含（不区分大小写）
__in  # 在给定的列表中
__startswith  # 以指定字符开始（区分大小写）
__istartswith  # 以指定字符开始（不区分大小写）
__endswith  # 以指定字符结束（区分大小写）
__iendswith  # 以指定字符结束（不区分大小写）
__range  # 在两个值之间（包含边界）

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
