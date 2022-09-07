# Nginx

官方文档：<https://nginx.org/en/docs/beginners_guide.html>

```bash
# 安装
apt install nginx  # ubuntu
yum install nginx  # centos
brew install nginx  # mac

nginx -h  # 查看帮助
nginx -V  # 查看版本及配置变量
nginx -t  # 查看配置文件是否正常，会返回配置文件的路径

# 安装完后已经在运行了，查看状态
systemctl status nginx

# 启动
# systemctl start nginx.service
nginx
# 重启
# systemctl restart nginx.service
nginx -s reload
# 停止
# systemctl stop nginx.service
nginx -s quit  # 退出前先处理完已经接受的连接请求
nginx -s stop  # 直接退出

ps aux | grep nginx  # 查看进程
netstat -a | grep 80  # 查看端口
```

![20210919194707](http://image.zuoright.com/20210919194707.png)

## 配置文件

默认配置在：`/etc/nginx/sites-available/default`

每条指令以分号结尾

```ini
"""
全局块
"""
user [user] [group];  # 定义运行nginx服务器的用户和用户组
worker_processes auto;  # 允许生成的进程数
pid /run/nginx.pid;  # 进程id存放路径
error_log /usr/local/nginx/logs/error.log info;  # 错误日志路径及类型

"""
events块
"""
events {
    use epoll;  # 内核模型：select、poll、epoll
    worker_connections 768;  # 单个进程最大链接数
}

"""
http块
"""
http {
    """
    http 全局块
    """
    include /etc/nginx/conf.d/*.conf;  # 其它配置文件引入
    default_type application/octet-stream;  # 默认文件类型
    charset utf-8;  # 默认编码
    sendfile on;  # 优化静态资源

    """
    http server块
    可以有多个，每一块就相当于一台虚拟主机，用于对不同的网站做不同的配置
    通常server配置在独立的/etc/nginx/conf.d/*.conf中，通过引用的方式调用
    """
    server {
        listen 80;  # 监听端口，默认80
        server_name zuoright.com www.zuoright.com;  # 域名，多个由空格分隔
        index index.html index.htm index.php;  # 首页
        # autoindex on;  # 开启目录浏览

        ; 允许跨域
        ; add_header Access-Control-Allow-Origin *;
        ; add_header Access-Control-Allow-Credentials true;
        ; add_header Access-Control-Allow-Methods GET,POST;

        """
        location块
        每个server层下面允许有多个location，用于对不同的路径进行不同模块的配置
        格式：location [匹配符] uri
        ~匹配符后可跟正则表达式(区分大小写)
        """
        location / {  # 控制网站访问路径
            root html;  # root定义location的根路径
            """
            nginx欢迎页默认存放在--prefix路径下，使用nginx -V可以查看
            linux：--prefix=/usr/share/nginx
            mac：--prefix=/usr/local/Cellar/nginx/{version}

            alias /usr/share/nginx/  alias会替代location路径，只能在location块内使用，结尾必须加/？
            """
            index index.html index.htm;  # 默认首页文件
        }

        error_page 404 /404.html;  # 404页面
        error_page 500 502 503 504 /50x.html;  # 错误页面
        location = /40x.html {
            root html;  # 404重定向到首页
        }
    }

    """
    https配置
    """
    server {
        listen 443 ssl http2;
        server_name  localhost;

        ssl                      on;
        ssl_certificate          /etc/nginx/certs/example.crt;
        ssl_certificate_key      /etc/nginx/certs/example.key;

        ssl_session_timeout  5m;

        ssl_ciphers HIGH:!aNULL:!MD5;
        ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers   on;

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }
    }
}
```

## 正向代理

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

## 反向代理

- 真实服务器设置防火墙只允许代理服务器，访问提高安全性，让代理服务器分发请求
- 负载均衡
- Cache作用

```ini
server {
    listen 8888;  # 监听端口
    server_name  xxx.xxx.xxx.xxx;  # 代理服务器IP或域名

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
访问xxx.xxx.xxx.xxx:8888/one，转发给127.0.0.1:8080
访问xxx.xxx.xxx.xxx:8888/two，转发给127.0.0.1:8081
"""
```

## 透明代理

比较少见，通常用于公司内网行为管理，比如内网翻墙或者限制访问某些网站等

## 负载均衡

负载均衡就是反向代理服务器接收到客户端的请求后，以某种策略将原先请求负载集中到单个服务器上的情况改为将请求分发到多个服务器上

三种负载均衡策略：

- 轮询(默认)：每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器 down 掉，能自动剔除。
- 权重：权重默认为1，越大则被分配的客户端越多，即处理的请求越多
- ip_hash：每个请求按访问 ip 的 hash 结果分配，这样每个访客固定访问一个后端服务器，可以解决 session 的问题。

```ini
upstream mysite {
    sever 127.0.0.1:8080;  # 轮询
    sever 127.0.0.1:8081 weight=2;  # 权重
}

server {
    listen       8888 ;  # 监听端口
    server_name  xxx.xxx.xxx.xxx;  # 代理服务器IP或域名

    location /one {
        proxy_pass mysite;
        index index.html;
    }
}
```

## 动静分离

动静分离就是把很少会发生修改的诸如图像，视频，css样式等静态资源文件放置在单独的服务器上，而动态请求则由另外一台服务器上进行。

这样一来，负责动态请求的服务器则可以专注在动态请求的处理上，从而提高了我们程序的运行效率，与此同时，我们也可以针对我们的静态资源服务器做专属的优化，增加我们静态请求的响应速度。

通常使用 Nginx 处理静态页面，Tomcat 处理动态页面。

```ini
server {
    listen       8888 ;  # 监听端口
    server_name  xxx.xxx.xxx.xxx;  # 代理服务器IP或域名

    location / {
        root /html/;
        index index.html;
    }

    location ~ .*.(htm|html|gif|jpg|jpeg|png)$ {  # 拦截静态资源
        root /data/;  # 设置寻找资源的根目录
    }
}
```

## 参考

- <https://www.cnblogs.com/itzgr/tag/Nginx/>
- <https://www.w3cschool.cn/nginx/nginx-d1aw28wa.html>
