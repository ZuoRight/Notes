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

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}
```

