# Nginx

主要功能：

- 正向代理
- 反向代理
- 负载均衡
- 动静分离

```shell
# 安装
brew install nginx
# 查看版本
nginx -v
nginx -h

# 检查配置是否正确，并返回配置路径
nginx -t  # /usr/local/etc/nginx/nginx.conf

# 启动
nginx
# 重启
nginx -s reload
# 停止
nginx -s quit  # 退出前先完成已接受的连接请求
nginx -s stop  # 直接退出

# 获取nginx:master进程号
ps -ax | grep nginx
# 杀死进程
sudo kill -s QUIT 进程号
```

## 配置

- 全局块
- events块
- http块
  - http全局块
  - server块：相当于一个虚拟主机
    - server全局块
    - location块
