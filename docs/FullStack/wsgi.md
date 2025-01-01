# WSGI

WSGI（Web Server Gateway Interface）是 Python 应用程序和应用服务器之间的一种接口，已经被广泛接受，基于此封装的工具主要有：uWSGI、Gunicorn

WSGI 只支持同步代码，异步需要使用 ASGI，对应的封装工具有：Daphne、Hypercorn、Uvicorn

## uWSGI

uWSGI 是 C 语言实现的一款 WSGI 工具，它跟 Flask/Django 等主流框架都遵循了 WSGI 规范，自然可以与这些框架实现的应用通信，然后它可以用自带的 uwsgi 协议与 Ngnix 进行通信，最终 Nginx 通过 HTTP 协议对外提供服务

> 官方文档：<https://uwsgi.readthedocs.io/en/latest/Configuration.html>

```shell
# 安装
python -m pip install uwsgi
"""
--home 指定虚拟环境的绝对路径
--chdir 项目绝对路径
--wsgi-file 可以指定wsgi.py规范文件的路径

--http 使用uWSGI自带的http协议，方便测试，当和Nginx一起使用时，指定socket或http-socket即可
--master 指开启master管理进程，建议开启
--workers 指开启的worker进程数，用于处理请求
"""

# 命令行指定参数启动
uwsgi --http 127.0.0.1:3031 --file demo/wsgi.py

# 从配置文件启动，返回[uWSGI] getting INI configuration from uwsgi.ini
uwsgi --ini path/uwsgi.ini

uwsgi --reload path/uwsgi.pid  # 重启
uwsgi --stop path/uwsgi.pid  # 停止
```

```ini
[uwsgi]
; 项目路径
chdir=/app
; wsgi对象
module=demo.wsgi:application
; 主进程
master=True
; 保存进程号
pidfile=/tmp/uwsgi/demo-master.pid
; 保存日志
daemonize=/tmp/uwsgi/demo.log

; 使用http协议提供服务，测试时使用
; http=127.0.0.1:3030
; 使用socket协议，配合nginx使用，与http端口不能重复
socket=127.0.0.1:3031
```

## Gunicorn

纯 Python 编写的 WSGI 服务器，Fork 自 Ruby 的 Unicorn 项目，名字是 Green Unicorn 的缩写

官网：<https://gunicorn.org/>

安装：`pip install gunicorn`

- demo

```python
def app(environ, start_response):
    data = b"Hello, World!\n"
    start_response("200 OK", [
        ("Content-Type", "text/plain"),
        ("Content-Length", str(len(data)))
    ])
    return iter([data])
```

```shell
# 查看帮助
gunicorn -h

# 检查配置
gunicorn --print-config demo:app
"""
access_log_format                 = %(h)s %(l)s %(u)s %(t)s "%(r)s" %(s)s %(b)s "%(f)s" "%(a)s"
accesslog                         = None
backlog                           = 2048
bind                              = ['127.0.0.1:8000']
...                               = ...
"""

# 启动
"""
1.指定参数启动
    -w 4 或 --works=4 进程数
    -b 127.0.0.1:8000 或 --bind=host 绑定host
    -D 或 --daemon 后台运行
    --bind 127.0.0.1:8000
    --pid /tmp/gunicorn.pid 记录gunicorn进程号文件存放位置，方便进程管理
"""
gunicorn -w 4 demo:app

"""
2.指定变量启动
"""
GUNICORN_CMD_ARGS="--bind=127.0.0.1 --workers=3" gunicorn app:app

"""
3. 指定配置文件启动
"""
gunicorn -c gunicorn.conf.py demo:app
```

配置文件：`./gunicorn.conf.py`

> 可配置项参考：<https://docs.gunicorn.org/en/stable/settings.html#settings>

```python
import multiprocessing

bind = "127.0.0.1:8000"
workers = multiprocessing.cpu_count() * 2 + 1
```

- 启动 Django

```shell
gunicorn myproject.wsgi
"""
# 可以指定设置
--env DJANGO_SETTINGS_MODULE=myproject.settings.set_prod
"""
```
