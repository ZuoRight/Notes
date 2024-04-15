# Celery

<https://docs.celeryq.dev/en/stable/index.html>

`pip install celery`

Celery 是 Python 中最流行的异步消息队列框架，需要使用消息队列作为代理（Broker）与客户端相互通信，任务结果可以保存到不同的 Backend 中。

默认使用 RabbitMQ 作为 Broker，也可以使用 Redis。

通常使用 Redis 作为 Backend，RabbitMQ 借助 RPC 也可以作为 Backend 存储结果，或者借助 SQLAlchemy 存储到 PostgreSQL、MySQL。

- [Using RabbitMQ](https://docs.celeryq.dev/en/stable/getting-started/backends-and-brokers/rabbitmq.html)
- [Using Redis](https://docs.celeryq.dev/en/stable/getting-started/backends-and-brokers/redis.html)

## 运行 Celery Worker Server

```python
# tasks.py
from celery import Celery

# Celery库在使用前必须实例化，这个实例是线程安全的
app = Celery('tasks', broker='pyamqp://guest@localhost//')
"""
参数1: 当前模块名称
参数2: 指定broker的url
    rabbit(默认): amqp://localhost
    redis: redis://localhost 
"""

@app.task
def add(x, y):
    return x + y
```

运行一个或多个进程，监听消息队列，等待执行异步任务

```shell
celery -A myproject worker --loglevel=info
```

使用 Celery Flower 监视和管理 Celery 集群

```shell
pip install flower
celery -A myproject flower
```

## 调用任务

```python
from tasks import add

# delay() 是 apply_async() 方法的便捷快捷方式
add.delay(4, 4)
```

调用任务会返回一个 `AsyncResult` 实例，可用于检查任务的状态或获取返回值，默认不启用。

如果启用需要在实例化的时候配置 result backend

```python
# 任务结果保存到 RPC
app = Celery('tasks', backend='rpc://', broker='pyamqp://')

# 任务结果保存到 Redis
app = Celery('tasks', backend='redis://localhost', broker='pyamqp://')
```

```python
from tasks import add

result = add.delay(4, 4)

result.ready()  # 返回任务是否已完成
result.traceback  # 追溯任务异常
```

## Using Celery with Django

> <https://docs.celeryq.dev/en/stable/django/first-steps-with-django.html>

Django v3.1 已集成 Celery，无需单独安装

### 配置

在 `<myproject>/<myproject>/` 路径创建一个 `celery.py` 文件

```python
import os
from celery import Celery

# Set the default Django settings module for the 'celery' program.
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')

app = Celery('myproject')
# 使用 Django 配置模块作为 Celery 的配置源，配置项都以 CELERY_ 开头
app.config_from_object('django.conf:settings', namespace='CELERY')

# 使用 Redis 作为消息代理
# app.conf.broker_url = 'redis://localhost:6379/0'

# Load task modules from all registered Django apps.
app.autodiscover_tasks()


@app.task(bind=True, ignore_result=True)
def debug_task(self):
    print(f'Request: {self.request!r}')
```

### 初始化

将 `celery.py` 导入到 `<myproject>/<myproject>/__init__.py` 中，保证 Celery 应用和 Django 项目能够协同工作，同时确保 Celery 的各种设置和任务能够在 Django 项目启动时被正确加载和初始化

```python
from .celery import app as celery_app

__all__ = ('celery_app',)
```

### 编写异步任务

使用 `@shared_task` 装饰器允许创建任务，而无需任何具体的应用程序实例

`<myproject>/<myapp>/tasks.py`

```python
from celery import shared_task

@shared_task
def add(x, y):
    return x + y

@shared_task
def mul(x, y):
    return x * y
```

### 触发任务

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

### 在命令行中启动 Celery worker


```shell
```

