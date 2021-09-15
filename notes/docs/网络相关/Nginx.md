# Nginx

```bash
# 安装
apt install nginx  # ubuntu
yum install nginx  # centos

# 启动：nginx
systemctl start nginx.service
# 重启：nginx -s reload
systemctl restart nginx.service
# 停止：nginx -s quit/stop quit方式退出前会先处理完已经接受的连接请求
systemctl stop nginx.service

# 查看帮助
nginx-h
# 查看配置文件是否正常
nginx -t  # 会返回配置文件的路径
```

## 配置文件

- 全局块

```conf
user www-data;  # 配置运行nginx服务器的用户（组）
worker_processes auto;  # 允许生成的进程数
pid /run/nginx.pid;  # 进程pid存放路径
include /etc/nginx/modules-enabled/*.conf;  # 类型以及配置文件的引入
```

- events块

```conf
events {
    worker_connections 768;
    # multi_accept on;
}
```

- http块

```conf
http {
	server {
		# 监听来自指定IP，指定端口的请求
		listen 127.0.0.1:8000;
		"""
		# 不指定IP就默认监听所有IP
		listen 8000;  # 等价于：*:8000;
		# 不指定端口就默认监听80端口
		listen 127.0.0.1;  # 等价于：localhost:80;
		# IPv6地址需使用中括号括起来
		[fe80::1]
		"""
		# 虚拟主机名，即域名，多个由空格分隔
		server_name myserver.com www.myserver.com
	}
}
```

- server块

每个server块就相当于一台虚拟主机

```conf


# 
"""
匹配符
=   严格匹配
~   正则表达式，区分大小写
~*  正则表达式，忽略大小写
^~   找到匹配度最高之后立刻处理，不再继续解析正则
"""
location 匹配符 uri {
    root path  # 设置根目录
    }
```


