# Celery

- <https://docs.celeryq.dev/en/stable/index.html>
- <https://github.com/HanshengLi1993/celery-python>

`pip install celery`

Celery 是 Python 中最流行的分布式或异步任务队列框架，使用消息队列作为代理（Broker）与客户端相互通信，任务结果可以保存到不同的后端（Backend）。

![20240416171455](https://image.zuoright.com/20240416171455.png)

默认使用 RabbitMQ 作为 Broker，也可以使用 Redis。

通常使用 Redis 作为 Backend，RabbitMQ 借助 RPC 也可以作为 Backend 存储结果，或者借助 SQLAlchemy 存储到 PostgreSQL、MySQL。

- [Using RabbitMQ](https://docs.celeryq.dev/en/stable/getting-started/backends-and-brokers/rabbitmq.html)
- [Using Redis](https://docs.celeryq.dev/en/stable/getting-started/backends-and-brokers/redis.html)

## Celery Worker

### 添加 task

```python
# tasks.py
from celery import Celery

# Celery库在使用前必须实例化，这个实例是线程安全的
app = Celery('tasks', broker='pyamqp://guest@localhost//')
"""
参数1: 模块名或项目名，最好与 __main__ 保持一致，用于帮助定位任务
参数2: 指定broker的url，redis可以这样: redis://，等价于 redis://localhost:6379/0
"""
# app.conf.broker_url = 'redis://localhost:6379/0'  # 单独设置broker_url
# app.conf.result_backend = 'redis://localhost:6379/0'  # 单独设置result_backend


@app.task
def add(x, y):
    return x + y
```

### 运行 Server

运行一个或多个 Worker，监听消息队列，等待执行异步任务

```shell
# 运行2个Worker
celery -A tasks worker --loglevel=info -n worker1@%h  # -n或--hostname指定节点名，%h会被替换为主机名
celery -A tasks worker --loglevel=info -n worker2@%h

# 运行1个Worker，可以不指定节点名
celery -A tasks worker --loglevel=info  # 可以用concurrency=n指定进程数，默认与CPU数量相同

'
 -------------- celery@bogon v5.3.6 (emerald-rush)
--- ***** ----- 
-- ******* ---- macOS-14.2.1-arm64-arm-64bit 2024-04-15 23:56:39
- *** --- * --- 
- ** ---------- [config]
- ** ---------- .> app:         tasks:0x10254aa00
- ** ---------- .> transport:   amqp://guest:**@localhost:5672//
- ** ---------- .> results:     disabled://
- *** --- * --- .> concurrency: 8 (prefork)
-- ******* ---- .> task events: OFF (enable -E to monitor tasks in this worker)
--- ***** ----- 
 -------------- [queues]
                .> celery           exchange=celery(direct) key=celery

[tasks]
[2024-04-15 23:56:40,089: INFO/MainProcess] Connected to amqp://guest:**@127.0.0.1:5672//
[2024-04-15 23:56:40,096: INFO/MainProcess] mingle: searching for neighbors
[2024-04-15 23:56:41,141: INFO/MainProcess] mingle: all alone
[2024-04-15 23:56:41,175: INFO/MainProcess] celery@bogon ready.

调用任务时，这里将打印日志
[2024-04-16 00:14:31,215: INFO/MainProcess] Task tasks.add[16f3d49f-9174-4c55-9884-83f54db0f4b2] received
[2024-04-16 00:14:31,217: INFO/ForkPoolWorker-8] Task tasks.add[16f3d49f-9174-4c55-9884-83f54db0f4b2] succeeded in 0.0008349169999632977s: 5
'
```

### 调用 task

```python
from tasks import add

# delay() 是 apply_async() 方法的便捷快捷方式
add.delay(2, 3)  # <AsyncResult: ba0d3172-f2f1-42ee-9323-a78b9e9812d7>
```

因为任务是异步的，调用任务不会直接返回任务结果，而是会返回一个 `AsyncResult` 实例，可用于检查任务的状态或者获取返回值，默认不启用，需要在实例化的时候配置 Result Backend 才可以使用

```python
# 任务结果传到RPC，但RPC并不能保存，而是作为消息发送，只能检索一次
app = Celery('tasks', backend='rpc://', broker='pyamqp://')

# 任务结果保存到Redis
app = Celery('tasks', backend='redis://localhost', broker='pyamqp://')
# 设置存储到Redis的Key都加统一前缀，默认没有
app.conf.result_backend_transport_options = {
    'global_keyprefix': 'celery_test'
}
```

```python
from tasks import add

result = add.delay(2, 3)
result.ready()  # 返回任务是否已完成
result.get(timeout=1)  # 返回任务执行结果，通常不这么用，因为是异步的
result.traceback  # 追溯任务异常
```

![20240416115038](https://image.zuoright.com/20240416115038.png)

## Celery Beat

celery beat 是一个调度程序（scheduler），可以定期启动任务，然后由集群中的可用工作节点执行

### 添加 entry

要定期调用任务，需要添加一个 entry，默认从 beat_schedule 设置获取 entry，也可以从 SQL 读取

```python
app.conf.beat_schedule = {
    'add-every-30-seconds': {
        'task': 'tasks.add',  # 要执行的任务名称，tasks是任务所在模块名(tasks.py)
        'schedule': 30,  # 执行频率(秒)，可以是整数、timedelta、crontab形式
        'args': (2, 3)  # 传递给 add() 的参数
    },
}

# 默认使用 UTC 时区，Django 中默认使用 TIME_ZONE。
app.conf.timezone = 'Asia/Shanghai'
```

### 启动调度程序

Celery Beat 只是定时发送任务到消息队列中，实际执行这些任务的是 Celery Worker，所以启动 Beat 服务前记得先启动 Worker。

```shell
# 注意：任务结果会输出在 Worker 的日志中
celery -A tasks beat --loglevel=info  # tasks 是模块名(tasks.py)
'
celery beat v5.3.6 (emerald-rush) is starting.
__    -    ... __   -        _
LocalTime -> 2024-04-16 16:46:04
Configuration ->
    . broker -> amqp://guest:**@localhost:5672//
    . loader -> celery.loaders.app.AppLoader
    . scheduler -> celery.beat.PersistentScheduler
    . db -> celerybeat-schedule
    . logfile -> [stderr]@%INFO
    . maxinterval -> 5.00 minutes (300s)
[2024-04-16 16:46:04,124: INFO/MainProcess] beat: Starting...
[2024-04-16 16:46:04,162: INFO/MainProcess] Scheduler: Sending due task add-every-monday-morning (tasks.add)
[2024-04-16 16:46:34,152: INFO/MainProcess] Scheduler: Sending due task add-every-monday-morning (tasks.add)
'
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

运行 celery worker server：`celery -A myproject worker --loglevel=info`

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

## Flower

<https://flower.readthedocs.io/en/latest/index.html>

Flower 是一个基于 Web 的工具，用于监视和管理 Celery 集群(Clusters)

```shell
pip install flower

celery -A tasks flower --port=5001
```
