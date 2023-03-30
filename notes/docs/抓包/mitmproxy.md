# mitmproxy

官方文档：<https://docs.mitmproxy.org/stable/>

包含三部分

- mitmproxy 交互式命令行界面
- mitmdump 非交互式终端输出
- mitmweb 基于浏览器的 GUI

## 安装

```shell
# 方式1，最新版会默认安装依赖Py3.10
brew install mitmproxy
# 方式2
pip install mitmproxy

mitmproxy --version
"""
Mitmproxy: 8.1.1
Python:    3.10.6
OpenSSL:   OpenSSL 1.1.1q  5 Jul 2022
Platform:  macOS-12.5-arm64-arm-64bit
"""
```

## 基础

- 启动mitmdump

```shell
mitmdump

"""
-p 指定端口号，默认8080端口
-s 执行python脚本
"""
```

- 代理

可以使用SwitchyOmega设置一个情景模式，代理到8080端口

![20220826164930](http://image.zuoright.com/20220826164930.png)

- 安装证书

访问：<http://mitm.it>，下载对应系统的证书，然后安装到受信任的根证书列表

![20220826165049](http://image.zuoright.com/20220826165049.png)

- 验证：比如通过 <https://httpbin.org> 发送了一条`get`请求

![20220826165810](http://image.zuoright.com/20220826165810.png)

- 操作

`?` 可查看所有按键操作

`q` 返回/退出

使用上下方向键，切换聚焦流（左边标记`>>`的行）

然后可以回车（或直接鼠标点击）进入详情

![20220826170812](http://image.zuoright.com/20220826170812.png)

进入详情后，可通过左右方向键（或直接鼠标点击）在`Request, Response, Detail`三个Tab间切换
