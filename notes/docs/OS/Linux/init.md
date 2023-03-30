# 初始化守护进程

一个 Linux 操作系统，在系统打开电源，执行 BIOS/boot-loader 之后，就会由 boot-loader 负责加载 Linux 内核

> Linux 内核执行文件一般会放在 /boot 目录下，文件名类似 vmlinuz*

在内核完成了操作系统的各种初始化之后，这个程序需要执行的第一个用户态程就是 init 进程，也叫1号进程，于是系统从内核态切换到用户态，然后创建其他进程。

> 内核代码启动 1 号进程的时候，在没有外面参数指定程序路径的情况下，一般会从几个缺省路径尝试执行 1 号进程的代码

Linux init 进程最基本的功能就是创建出 Linux 系统中其他所有的进程，并且管理这些进程

## SysVinit

也叫 `System V init`, 简称 `init`

对应的管理工具：`service`

```shell
service network start/stop/restart xxx

chkconfig -list network
```

## Systemd

Systemd 是最新的守护进程系统，会尽可能启动较少的进程，尽可能更多进程并发启动，提高了系统的启动速度

目前主流的 Linux 发行版都会把/sbin/init 作为符号链接指向 Systemd

```shell
ls -l /sbin/init
"""
lrwxrwxrwx 1 root root 20 Jul 21 19:00 /sbin/init -> /lib/systemd/systemd
"""
```

![20230204012419](http://image.zuoright.com/20230204012419.png)

对应的管理工具：`systemctl`，负责检查和控制 systemd 系统和服务管理器

```shell
systemctl status xxx
systemctl start/stop/restart/reload xxx
systemctl enable/disable xxx  # 开机自启/关闭

systemctl list-unit-files xxx.service
```

## Supervisor

官网：<http://www.supervisord.org/>

Supervisor是用Python开发的一套通用的服务进程管理程序，通过fork/exec的方式把这些被管理的进程当作其子进程来启动，能将一个普通的命令行进程变为后台daemon，并监控进程状态，异常退出时能报警或自动重启。

只需要在Supervisor的配置文件中，把要管理的进程的可执行文件的路径写进去即可。还可以设置一个非root的user，这个user就可以管理它对应的进程

```shell
# 安装，不支持Windows
pip install supervisor

# 1.生成配置文件
echo_supervisord_conf > path_config

# 2.启动supervisord进程
supervisord -c path_config
"""
ps -ef | grep supervisord
kill -9 pid
"""

# 3.管理程序
supervisorctl start xxx     # 启动
supervisorctl stop xxx      # 停止
supervisorctl restart xxx   # 重启

supervisorctl status        # 查看所有进程的状态
supervisorctl update        # 配置文件修改后使用该命令加载新的配置
supervisorctl reload        # 重新启动配置中的所有程序
```

supervisord.conf

```ini
[program:gunicorn]
command=gunicorn demo.wsgi:application -c gunicorn.conf.py  # 启动Django的脚本
# directory=.  # 项目目录
# user=nobody  # 允许运行脚本的用户身份
autostart=true  # supervisor启动的时候是否随着同时启动，默认true
# 当程序exit的时候，有三个选项，true,false,unexpected（默认）
    # - true 自动重启
    # - false 无论什么情况下，都不会被重新启动，
    # - unexpected（默认） 只有当进程的退出码不在下面的exitcodes里面定义的
autorestart=false
redirect_stderr=true
# stderr_logfile=/tmp/test_stderr.log
# stdout_logfile=/tmp/test_stdout.log
```