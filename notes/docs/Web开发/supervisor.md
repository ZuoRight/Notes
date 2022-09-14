# Supervisor

官网：<http://www.supervisord.org/>

Supervisor是用Python开发的一套通用的进程管理程序，通过fork/exec的方式把这些被管理的进程当作其子进程来启动，能将一个普通的命令行进程变为后台daemon，并监控进程状态，异常退出时能报警或自动重启。

只需要在Supervisor的配置文件中，把要管理的进程的可执行文件的路径写进去即可。还可以设置一个非root的user，这个user就可以管理它对应的进程

```bash
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
