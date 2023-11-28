# Admin

> 参考：<https://docs.djangoproject.com/zh-hans/4.0/ref/contrib/admin/>

Django Admin 是一个预构建的应用

## 注册应用

`admin.py`

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

    readonly_fields = ('my_field',)  # 设置字段为只读

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

## 创建管理员账号

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
python manage.py shell  # 可以安装ipython扩展使用体验更好的shell_plus

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

## 后台任务

`pip install celery redis`

- 配置

```python
# myproject/celery.py
import os
from celery import Celery

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')

app = Celery('myproject')

app.config_from_object('django.conf:settings', namespace='CELERY')

# 使用 Redis 作为消息代理
# app.conf.broker_url = 'redis://localhost:6379/0'

app.autodiscover_tasks()
```

- 初始化

保证 Celery 应用和 Django 项目能够协同工作

同时确保 Celery 的各种设置和任务能够在 Django 项目启动时被正确加载和初始化

```python
# myproject/__init__.py
from .celery import app as celery_app

__all__ = ('celery_app',)
```

- 编写异步任务

```python
# myapp/tasks.py
from myproject.celery import app
from time import sleep

@app.task
def generate_user_report(user_id):
    # 模拟一个耗时的报告生成过程
    sleep(10)
    print(f"Report generated for user: {user_id}")  # 实际中这里会有生成报告的逻辑
```

- 触发任务

```python
# views.py 或其他适当的地方
from .tasks import generate_user_report

# 用户注册时触发任务
def user_signup(request):
    # ...
    # 使用 .delay() 来异步执行任务
    """
    delay 是 apply_async 的简化版本，立即将任务发送到队列中
    只接受任务的参数，不带任何额外的选项或参数
    """
    generate_user_report.delay(user.id)

# 使用 .apply_async() 来异步执行任务
result = my_task.apply_async(link=on_task_success.s())

"""
使用 .delay() 或 .apply_async() 触发 Celery 任务时，会返回一个 AsyncResult 实例。
这个对象可以用来查询任务的状态和结果
"""
# 使用回调函数，在任务成功执行后自动调用
@app.task
def on_task_success(result):
    print(f"Task completed successfully with result: {result}")
```

- 在命令行中启动 Celery worker

运行一个或多个进程，监听消息队列，等待执行异步任务

```shell
celery -A myproject worker --loglevel=info
```

- 使用 Celery Flower 监视和管理 Celery 集群

```shell
pip install flower

celery -A myproject flower
```
