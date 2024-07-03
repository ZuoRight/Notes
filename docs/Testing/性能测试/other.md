# other

![20230902230811](https://image.zuoright.com/20230902230811.png)

> 平均负载与 CPU 使用率不一定对应，最理想的就是每个 CPU 上都刚好运⾏着⼀个进程，这样每个 CPU 都得到了充分利⽤

## stress

```shell
# 压测工具，可用来模拟各种异常进程
apt install stress

# CPU 密集型进程（此时CPU 使⽤率与LA是一致的）
stress --cpu 1 --timeout 600  # 模拟⼀个 CPU 使⽤率 100% 的场景
# I/O 密集型进程（此时 CPU 使⽤率不一定升高）
stress -i 1 --timeout 600  # 模拟 I/O 压⼒（不停地执⾏ sync）
# ⼤量进程的场景
stress -c 8 --timeout 600  # 模拟8个进程
```

## sysstat

```shell
# 分析工具
apt install sysstat

# mpstat，多核 CPU 性能分析⼯具，⽤来实时查看每个 CPU 的性能指标以及平均指标
mpstat -P ALL 5  # -P ALL 表示监控所有CPU，后面数字5表示间隔5秒后输出一组数据
# pidstat，进程性能分析⼯具，⽤来实时查看进程的 CPU、内存、I/O 以及上下⽂切换等性能指标
pidstat -u 5 1  # 查看每个进程的状态，看是哪个导致了 CPU 使⽤率过载
# iostat
```
