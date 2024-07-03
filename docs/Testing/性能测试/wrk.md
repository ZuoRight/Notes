# wrk

<https://github.com/wg/wrk>

一款 HTTP 基准测试工具

基于系统自带的高性能 I/O 机制，如 epoll, kqueue，利用 ae 异步的事件驱动框架，通过很少的线程就可以压出很大的并发量

- epoll 是 Linux 系统中用于处理大量文件描述符的 I/O 事件通知机制
- kqueue 是一个功能类似于 epoll 的事件通知机制，但是设计用于 BSD 和 macOS 系统

## 安装

仅支持类 Unix 系统

- macOS

```shell
brew install wrk
```

- Ubuntu

```shell
sudo apt install build-essential libssl-dev git -y
git clone https://github.com/wg/wrk.git wrk
cd wrk
make
sudo cp wrk /usr/local/bin  # 将可执行文件移动到 /usr/local/bin 位置
```

## 使用

```shell
wrk -v

# 基准测试：使用 12 个线程，并保持 400 个 HTTP 连接打开，运行 30 秒
wrk -t12 -c400 -d30s http://127.0.0.1:8080/index.html
'
Running 30s test @ http://127.0.0.1:8080/index.html
  12 threads and 400 connections

    Thread Stats   Avg      Stdev     Max   +/- Stdev
                 平均值      标准差    最大值  正负一个标准差所占比例
Latency 延迟     635.91us    0.89ms  12.92ms   93.69%
Req/Sec 每秒请求数 56.20k     8.07k   62.00k    86.54%
    
  22464657 requests in 30.00s, 17.76GB read

Requests/sec: 748868.53
Transfer/sec: 606.33MB  
'
```

通过参数 `--script` 指定 Lua 脚本，来满足个性化需求

参考：<https://www.cnblogs.com/quanxiaoha/p/10661650.html>
