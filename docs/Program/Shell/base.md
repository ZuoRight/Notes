# 常用命令

显示当前系统中的所有网络设备，即网卡列表

```shell
ip addr  # iproute2

ifconfig  # net-tools

ipconfig /all  # windows
```

```shell
netstat -a | grep 8080  # 查看端口
ps -ax | grep nginx  # 查看进程
kill -s QUIT pid  # 结束进程
```
