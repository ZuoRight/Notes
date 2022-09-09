# CGI

Web框架（Django、Flask）需要通过一套接口协议（CGI）与应用服务器（uWSGI、Tomcat）交互，通过应用服务器来接收并解析HTTP请求。

CGI(Common Gateway Interface)是早期Web开发的接口规范，FastCGI为其增强版

某些编程语言有独立的规范，比如

- Java: `Servlet`(Server Applet)，由于提出的较早，比较复杂
- Python: `WSGI`(Web Server Gateway Interface)，接近CGI，比较简单

WSGI Server或者Servlet容器通常被称之为应用服务器，Nginx和Apache等通常被称之为Web服务器，或者HTTP服务器

![20220908142430](http://image.zuoright.com/20220908142430.png)

客户端发出一个HTTP请求，到达应用程序，为了提高性能，可以加一层Nginx反向代理处理静态资源，还可以做负载均衡等

## Servlet

Java Web 相关的标准都是在EE中定义的，Servlet API是一个jar包，通过构建工具引入它，与其他一些文件按固定结构组织并打包为.war文件

> `war`(Java Web Application Archive) 构建后的Web应用程序

普通的Java程序是通过启动JVM，然后执行main()方法开始运行。

但是Web应用程序，需要启动应用服务器（也称为Servlet容器）加载war包来运行Servlet，常用的Servlet容器有：

- `Jetty`：由Eclipse开发的开源免费服务器
- `Tomcat`：由Apache开发的开源免费服务器
- `GlassFish`：一个开源的全功能JavaEE服务器

## WSGI

WSGI是Python应用程序和应用服务器之间的一种接口，已经被广泛接受，基于此封装的工具主要有：uWSGI、Gunicorn

> WSGI只支持同步代码，异步需要使用ASGI，对应的封装工具有：Daphne、Hypercorn、Uvicorn

## Gunicorn

纯Python编写的WSGI服务器，Fork自Ruby的Unicorn项目，名字是Green Unicorn的缩写

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

```bash
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

- 启动Django

```bash
gunicorn myproject.wsgi
"""
# 可以指定设置
--env DJANGO_SETTINGS_MODULE=myproject.settings.set_prod
"""
```

## uWSGI

uWSGI是C语言实现的一款WSGI工具，它跟Flask/Django等主流框架都遵循了WSGI规范，自然可以与这些框架实现的应用通信，然后它可以用自带的uwsgi协议与Ngnix进行通信，最终Nginx通过http协议对外提供服务

> 官方文档：<https://uwsgi.readthedocs.io/en/latest/Configuration.html>

```bash
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

## 使用Nginx反向代理和动静分离

以Django举例，当使用内置WSGIServer，或Gunicorn，或uWSGI启动服务后运行在8000端口

此时可以再加一层Nginx做反向代理提高安全性，同时可以把静态文件分离出来提高性能

![20220908232212](http://image.zuoright.com/20220908232212.png)

```ini
# djanog.conf
server {
    listen 80;
    server_name localhost;
    ; access_log  /var/log/nginx/example.log;

    ; """动静态分离"""
    location /static {
        alias  /path/demo/static_cdn;
    }

    ; """
    ; 如果使用内置WSGIServer或者Gunicorn，则直接使用支持http协议的proxy_pass指令设置即可
    ; """
    location / {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    ; """
    ; 如果使用uWSGI，需要通过自主实现的uwsgi协议与Nginx通信
    ; 此时要用uwsgi_pass指令
    ; """
    location / {
        include   uwsgi_params;
        uwsgi_pass  http://localhost:8000;  # 与uwsgi.ini中socket字段配置一致
    }
```

配置好后，重新加载Nginx配置，访问：<http://localhost>即可自动代理到<http://localhost:8000>

## Supervisor

Supervisor是用Python开发的一套通用的进程管理程序，能将一个普通的命令行进程变为后台daemon，并监控进程状态，异常退出时能自动重启。

它是通过fork/exec的方式把这些被管理的进程当作supervisor的子进程来启动，这样只要在supervisor的配置文件中，把要管理的进程的可执行文件的路径写进去即可。也实现当子进程挂掉的时候，父进程可以准确获取子进程挂掉的信息的，可以选择是否自己启动和报警。supervisor还提供了一个功能，可以为supervisord或者每个子进程，设置一个非root的user，这个user就可以管理它对应的进程。

```bash
pip install supervisor

# 1.生成配置文件
echo_supervisord_conf > /etc/supervisord.conf

# 2.启动supervisord进程
supervisord -c /etc/supervisord.conf

# 3.重启xxx项目
supervisorctl -c /etc/supervisord.conf restar xxx

# 4.管理程序（xxx = 某个程序名，或者all，即所有程序）
supervisorctl -c /etc/supervisord.conf start xxx
supervisorctl -c /etc/supervisord.conf stop xxx
supervisorctl -c /etc/supervisord.conf restart xxx
```

```supervisord.conf
command=uwsgi命令
```

```uwsgi.ini
[uwsgi] /home/...
```
