# Schedules

周期性任务（Periodic Tasks）可以通过不同类型的调度器来配置

### Interval

按照固定的时间间隔执行任务，比如每几分钟，每几小时

```python
from datetime import timedelta

timedelta(seconds=300)  # 300 seconds = 5 minutes
```

## Crontab

适合那些需要在特定时间执行的任务，如每天凌晨、每周一次等

<https://docs.celeryq.dev/en/stable/userguide/periodic-tasks.html#crontab-schedules>

```python
from celery.schedules import crontab

crontab(hour=7, minute=30, day_of_week=1),  # every Monday morning at 7:30 a.m.
```

Gitlab 管道计划用的也是 Crontab：<https://docs.gitlab.com/ee/topics/cron/index.html>

```shell
# ┌───────────── minute (0 - 59)
# │ ┌───────────── hour (0 - 23)
# │ │ ┌───────────── day of the month (1 - 31)
# │ │ │ ┌───────────── month (1 - 12)
# │ │ │ │ ┌───────────── day of the week (0 - 6) (Sunday to Saturday)
# │ │ │ │ │
# │ │ │ │ │
# │ │ │ │ │
# * * * * * <command to execute>

# * 表示 every
0 18 * * *  # 每天下午六点
```

## Solar

它使用地理位置（纬度和经度）来计算太阳事件的时间，适合需要根据太阳事件（如日出或日落）来执行任务的应用，比如调整灯光、温度控制等。

<https://docs.celeryq.dev/en/stable/userguide/periodic-tasks.html#solar-schedules>

```python
from celery.schedules import solar

# solar(event, 纬度latitude, 经度longitude)
solar('sunset', -37.81753, 144.96715),  # sunset in Melbourne
```

## Clocked

在指定的确切时间执行一次性任务

在 Celery 中，使用 Clocked 进行任务调度需要 `pip install django-celery-beat` 扩展

在 Django shell 中配置，`python manage.py shell`

```python
from django_celery_beat.models import PeriodicTask, ClockedSchedule
import datetime

# 设置确切的执行时间
clocked_time = datetime.datetime(2024, 1, 1, 9, 30, 0)  # 2024年1月1日上午9点30分

# 创建一个 ClockedSchedule 实例
clocked = ClockedSchedule.objects.create(clocked_time=clocked_time)

# 创建一个周期性任务
task = PeriodicTask.objects.create(
    name='Run my task at 2024-01-01 09:30:00',
    task='tasks.my_task',
    clocked=clocked,  # 关联我们的 ClockedSchedule
    one_off=True  # 确保任务只执行一次
)

# 保存任务
task.save()
```
