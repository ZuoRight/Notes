# Django 部署

## uWsgi

`pip install uwsgi`

新建：`uwsgi.ini`

```ini
[uwsgi]
; 项目根目录
chdir = /Users/7c/zuoright/onestep_django
; 指定wsgi模块下的application对象
module = onestep.wsgi:application
; 对本机3031端口提供服务
socket = 127.0.0.1:3031
; 主进程
master = true


; 以下字段非必需

; 退出、重启时清理文件
vacuum = true
max-requests=5000

; 日志
; daemonize=/tmp/uwsgi.log

; pid文件，用于下脚本启动、停止该进程
; pidfile=/tmp/uwsgi.pid

```

直接用uwsgi启动django，成功后访问：<http://127.0.0.1:3031>

```shell
# 启动，ctrl+c退出
uwsgi --http :3031 --file onestep/wsgi.py

# 查看进程
ps -ax | grep uwsgi
```

## Nginx

```shell
# 安装
# 配置文件路径：/usr/local/etc/nginx/nginx.conf
brew install nginx

nginx -v

# 测试配置是否成功
nginx -t -c /usr/local/etc/nginx/nginx.conf

# 启动
sudo nginx
# 重启
sudo nginx -s reload
# 停止
sudo nginx -s stop

# 获取nginx:master进程号
ps -ax | grep nginx

# 杀死进程
sudo kill -s QUIT 进程号
```

nginx+uwsgi方式启动项目

修改`nginx.conf`配置

```conf
server {
    listen       8080;
    server_name  localhost;

    location / {
        include   uwsgi_params;
        uwsgi_pass  127.0.0.1:3031;  和上面uwsgi配置的端口一致
    }

    location /static {
        alias  /Users/7c/zuoright/onestep_django/static_cdn;  配置静态文件路径
    }
```

启动nginx：`nginx`

启动uwsgi：`uwsgi --ini path/uwsgi.ini`

成功后访问：<http://127.0.0.1:8000>
