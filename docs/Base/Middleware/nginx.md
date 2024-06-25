# Nginx

官方文档：<https://nginx.org/en/docs/>

Nginx 是支持异步 I/O 的 Web 服务器，它在单核 CPU 上采用单进程模型就可以高效地支持多任务，在多核 CPU 上，可以运行多个进程（数量与 CPU 核心数相同），充分利用多核 CPU

```shell
apt install nginx  # ubuntu
yum install nginx  # centos
brew install nginx # mac

nginx -h  # 查看帮助
nginx -v  # 查看版本
nginx -t  # 查看配置是否正常，会返回配置文件的路径
```

```shell
nginx  # 启动，启动后可已通过命令来控制：nginx -s 信号
nginx -s reload  # 重新加载配置
nginx -s stop  # 直接退出
nginx -s quit  # 退出前先处理完已经接受的连接请求

# systemctl管理
systemctl status nginx  # 查看状态
systemctl start/restart/stop nginx.service # 启动/重启/停止

# brew管理（mac）
brew services start/resart/stop nginx
brew info nginx
"""
==> nginx: stable 1.23.1 (bottled), HEAD
HTTP(S) server and reverse proxy, and IMAP/POP3 proxy server
https://nginx.org/
/opt/homebrew/Cellar/nginx/1.23.1 (26 files, 2.2MB) *  安装目录
  Poured from bottle on 2022-09-08 at 20:09:23
From: https://github.com/Homebrew/homebrew-core/blob/HEAD/Formula/nginx.rb  安装来源
License: BSD-2-Clause
==> Dependencies
Required: openssl@1.1 ✔, pcre2 ✔
==> Options
--HEAD
    Install HEAD version
==> Caveats
Docroot is: /opt/homebrew/var/www  根目录

The default port has been set in /opt/homebrew/etc/nginx/nginx.conf to 8080 so that  配置文件及默认端口
nginx can run without sudo.

nginx will load all files in /opt/homebrew/etc/nginx/servers/.

To restart nginx after an upgrade:
  brew services restart nginx
Or, if you don't want/need a background service you can just run:
  /opt/homebrew/opt/nginx/bin/nginx -g daemon off;
==> Analytics
install: 37,590 (30 days), 124,647 (90 days), 469,949 (365 days)
install-on-request: 37,527 (30 days), 124,416 (90 days), 469,020 (365 days)
build-error: 1 (30 days)
"""
```

```shell
netstat -a | grep 8080  # 查看端口
ps -ax | grep nginx  # 查看进程
kill -s QUIT pid  # 结束进程
```

## 配置文件

默认的配置文件为：`nginx.conf`，通常位于以下路径，可使用`nginx -t`检查

- Linux：`/etc/nginx/...`，自定义配置可以放入conf.d下面
- Mac(Intel): `usr/local/etc/nginx/...`，自定义配置可以放入`servers/`路径下
- Mac(Apple): `/opt/homebrew/etc/nginx/...`，自定义配置可以放入`servers/`路径下

![20210919194707](http://image.zuoright.com/20210919194707.png)

默认的根目录，欢迎页`index.html`通常位于以下路径

- linux: `/var/www/html`
- mac: `/opt/homebrew/var/www`

指令分为块指令和简单指令，块指令又被称之为上下文，用大括号包含简单指令，亦可嵌套，每行指令以分号结尾

- events
- http
- server
- location

```ini
; user [user] [group];  # 定义运行nginx服务器的用户和用户组
worker_processes auto;  # 允许生成的进程数
; pid /run/nginx.pid;  # 进程id存放路径
; error_log /usr/local/nginx/logs/error.log info;  # 错误日志路径及类型
; include /etc/nginx/modules-enabled/*.conf;  # 其他配置

events {
    ; use epoll;  # 内核模型：select、poll、epoll
    worker_connections 1024;  # 单个进程最大链接数
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;  # 默认文件类型
    ; charset utf-8;  # 默认编码
    ; sendfile on;  # 优化静态资源

    """
    可以有多个server块，每一块就相当于一台虚拟主机，用于对不同的网站做不同的配置
    通常server配置在独立的/etc/nginx/conf.d/*.conf中，通过引用的方式调用
    当nginx接到请求后，会将请求携带的host:port跟server_name:listen相匹配
    """
    server {
        listen 80;  # 监听端口，Linux一般默认80，Mac一般默认8080
        server_name zuoright.com www.zuoright.com;  # 域名，多个由空格分隔
        index index.html index.htm index.php;  # 首页
        ; autoindex on;  # 开启目录浏览

        # 允许跨域
        ; add_header Access-Control-Allow-Origin *;
        ; add_header Access-Control-Allow-Credentials true;
        ; add_header Access-Control-Allow-Methods GET,POST;

        """
        location块
        每个server层下面允许有多个location，用于对不同的路径进行不同模块的配置
        格式：location [匹配符] uri
                匹配符～，可接正则表达式(区分大小写)
        """
        location / {  # 控制网站访问路径
            root html;  # root定义location的根路径
            """
            alias /usr/share/nginx/  alias会替代location路径，只能在location块内使用，结尾必须加/？
            """
            index index.html index.htm;  # 默认首页文件，先找根目录下的index.html，如果没有再找index.htm
        }

        error_page 404 /404.html;  # 404页面
        error_page 500 502 503 504 /50x.html;  # 错误页面
        location = /50x.html {
            root html;  # 重定向到首页
        }
    }

    """https配置"""
    ; server {
    ;     listen 443 ssl http2;
    ;     server_name  localhost;

    ;     ssl                      on;
    ;     ssl_certificate          /etc/nginx/certs/example.crt;
    ;     ssl_certificate_key      /etc/nginx/certs/example.key;

    ;     ssl_session_timeout  5m;

    ;     ssl_ciphers HIGH:!aNULL:!MD5;
    ;     ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2;
    ;     ssl_prefer_server_ciphers   on;

    ;     location / {
    ;         root   /usr/share/nginx/html;
    ;         index  index.html index.htm;
    ;     }
    ; }

    # 其它配置文件引入
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

## 动静分离

![20220909112752](http://image.zuoright.com/20220909112752.png)

动静分离就是把很少会发生修改的静态资源文件放置在单独的服务器上，而动态请求则由另外一台服务器上进行。

如此便可以针对静态资源服务器做专属优化，增加静态请求的响应速度，负责动态请求的服务器则可以专注在动态请求的处理上，从而提高运行效率。

```ini
server {
    listen       8080 ;  # 监听端口
    server_name  localhost;  # 代理服务器IP或域名

    location / {
        root html;
        index index.html index.htm;
    }

    # 拦截静态资源
    location ~ .*.(htm|html|gif|jpg|jpeg|png)$ {
        root /data/;  # 设置寻找资源的根目录
    }

    # 动态请求匹配到path为/node的就转发到8002端口处理
    location /node/ {  
        proxy_pass http://localhost:8002; # 反向代理，要用带协议头的完整URL
    }
}
```

## 正向代理

![20220909125225](http://image.zuoright.com/20220909125225.png)

- 实现授权控制，比如在家里使用VPN访问公司内网办公
- 客户端访问本来无法访问的原始服务器的资源，比如科学上网
- 加速访问原始服务器的资源，比如网游加速器
- 匿名代理，客户端知道但服务端不知道代理服务器的存在，比如肉鸡(黑客发起DDos攻击使用的傀儡机)
- Cache作用

```ini
server {
    resolver 8.8.8.8;  # 配置DNS解析IP地址
    resolver_timeout 30s;  # 超时时间
    listen 8888;  # 监听端口

    location /one {
        proxy_pass http://$http_host$request_uri;  # 配置正向代理参数，均是由 Nginx 变量组成
        index index.html;
    }
}
"""
浏览器代理配置
"""
```

## 透明代理

比较少见，通常用于公司内网行为管理，位于网络路由设备上，比如内网翻墙或者限制访问某些网站等

## 反向代理

- 设置防火墙只暴露代理服务器，让代理服务器分发请求，提高访问安全性
- 负载均衡
- 实现跨域
- Cache作用

> 抓包工具就属于反向代理

代理http协议，使用`proxy_pass`指令，代理uwsgi协议，使用`uwsgi_pass`指令

```ini
server {
    listen 8888;  # 监听端口
    server_name  localhost;  # 代理服务器IP或域名

    location /one {
        proxy_pass http://127.0.0.1:8080;  # 被代理服务器1的IP及端口
        index index.html;
    }

    location /two {
        proxy_pass http://127.0.0.1:8081;  # 被代理服务器2的IP及端口
        index index.html;
    }
}
"""
访问localhost:8888/one，转发给127.0.0.1:8080
访问localhost:8888/two，转发给127.0.0.1:8081
"""
```

## 负载均衡

负载均衡就是反向代理服务器接收到客户端的请求后，以某种策略将原先请求负载集中到单个服务器上的情况改为将请求分发到多个服务器上

三种负载均衡策略：

- 轮询(默认)：每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器 down 掉，能自动剔除。
- 权重：权重默认为1，越大则被分配的客户端越多，即处理的请求越多
- ip_hash：每个请求按访问 ip 的 hash 结果分配，这样每个访客固定访问一个后端服务器，可以解决 session 的问题。

```ini
upstream domain {
    sever localhost:8080;  # 轮询
    sever localhost:8081 weight=2;  # 权重
}

server {
    listen       8080 ;
    server_name  localhost;

    location /one {
        proxy_pass domain;
        index index.html;
    }
}
```

## 使用 Nginx 反向代理和动静分离

以 Django 举例，当使用内置 WSGIServer，或 Gunicorn，或 uWSGI 启动服务后运行在 8000 端口

此时可以再加一层 Nginx 做反向代理提高安全性，同时可以把静态文件分离出来提高性能

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

配置好后，重新加载 Nginx 配置，访问：<http://localhost> 即可自动代理到 <http://localhost:8000>

## 参考

- <https://www.cnblogs.com/itzgr/tag/Nginx/>
- <https://www.w3cschool.cn/nginx/nginx-d1aw28wa.html>
