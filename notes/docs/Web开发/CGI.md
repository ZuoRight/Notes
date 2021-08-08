# Web Server Gateway Interface

Web开发的接口规范，从最早的CGI(Common Gateway Interface)到各个语言独立的规范，比如Python的WSGI(Web Server Gateway Interface)和Java的Servlet(Server Applet)，WSGI接近CGI，比较简单，但Servlet由于提出的较早，要复杂得多。

## uWSGI

uWSGI是C语言实现的一款工具，它跟Flask/Django等主流框架都遵循了WSGI规范，自然可以与这些框架实现的应用通信，然后它可以用自带的uwsgi协议与Ngnix进行通信，最终Nginx通过http协议对外提供服务

```text
pip install uwsgi

常用参数：
--home 指定虚拟环境的绝对路径
--chdir 项目绝对路径
--wsgi-file 可以指定wsgi.py规范文件的路径

--http 使用uWSGI自带的http协议，方便测试，当和Nginx一起使用时，指定socket或http-socket即可
--master 指开启master管理进程，建议开启
--workers 指开启的worker进程数，用于处理请求
```

supervisor是python实现的一个进程管理工具，可以用pip安装，常用于自启动python web服务

```text
pip install supervisor

supervisord 可理解为supervisor服务器
supervisorctl 可理解为supervisor客户端

1. 生成配置文件
echo_supervisord_conf > /etc/supervisord.conf

2. 启动supervisord进程
supervisord -c /etc/supervisord.conf

3. 重启xxx项目
supervisorctl -c /etc/supervisord.conf restar xxx

4. 管理程序（xxx = 某个程序名，或者all，即所有程序）
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

## Servlet

Servlet API是一个jar包，通过构建工具引入它，与其他一些文件按固定结构组织并打包为.war文件

> war(Java Web Application Archive) 构建后的Web应用程序
>
> Java Web 相关的标准都是在 EE 中定义的

普通的Java程序是通过启动JVM，然后执行main()方法开始运行。

但是Web应用程序，需要启动Web服务器（也称为Servlet容器）加载war包来运行Servlet，常用的Servlet容器有：

- Jetty：由Eclipse开发的开源免费服务器
- Tomcat：由Apache开发的开源免费服务器
- GlassFish：一个开源的全功能JavaEE服务器
