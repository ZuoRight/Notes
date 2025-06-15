# WireShark

- 官网：<https://www.wireshark.org/>
- 官方文档：<https://www.wireshark.org/docs/wsug_html_chunked/ChapterUsing.html>

Wireshark is one of the best network packet analyzer. 工作在数据链路层，直接与网卡进行数据报文交换。

1998 年左右由 Gerald Combs 开发，原名 [Ethereal](https://www.wireshark.org/docs/wsug_html_chunked/ChIntroHistory.html)，随后不断加入新的开发者，2006 年更名并交由 Wireshark 团队管理，2008 年推出 1.0 版本，2015 年推出 2.0 版本。

Wireshark 抓包数据流

```text
Wireshark → libpcap → 内核网络栈 → 内核驱动 → 网卡(NIC)

pcap 是 Unix 系统用于捕获网络流量的 API
libpcap，即 packet capture library，是 Unix-like 系统的 Pcap API 实现
Npcap 是 Pcap API 的 Windows 实现，是 WinPcap（已不再维护）的改进版

内核网络栈（接口）
    MacOS 系统：BPF(Berkeley Packet Filter)
    Linux 系统：
        传统路径：BPF、AF_PACKET
        高性能路径：eBPF、PF_PACKET、XDP

在 MacOS 中，BPF 设备文件以 /dev/bpf* 的形式存在，即 libpcap 通过 /dev/bpf0、/dev/bpf1 等访问 BPF，默认只允许 root 用户或 wheel 组成员访问
受限于 MacOS 的 SIP 机制，手动修改权限会在重启后失效，所以需要通过 ChmodBPF.pkg 来获取持久化的 /dev/bpf* 的权限，使普通用户也能访问
```

Mac 安装包

![20210809131330](https://image.zuoright.com/20210809131330.png)

选择网卡，双击进入抓包页面

![20210809131957](https://image.zuoright.com/20210809131957.png)

![20250608131032](https://image.zuoright.com/20250608131032.png)

在 `View/Internals/Supported Protocols` 菜单下可以看到 WireShark 支持的所有协议以及支持的过滤属性等

![20210809165259](https://image.zuoright.com/20210809165259.png)

## 过滤器

面对 Wireshark 抓取的海量数据无从下手，就需要使用过滤器

### 捕获过滤器

只捕获符合条件的数据包

Capture -> Capture Filters

比如只捕获指定 host 的流量

> host 地址可以在目标网站的请求头中找到

![20250607185700](https://image.zuoright.com/20250607185700.png)

### 显示过滤器

根据条件过滤已经捕获的数据包

```text
and 或者 &&
or
eq 或者 == 完全匹配
contains 模糊匹配

ws.col.info contains "200 OK"

http.host contains "example.com"
http.request.method == "GET"

tcp.port == 80
tcp.stream == index

ip.addr == xxx.xx.xx.x
```

Wireshark 为了方便跟踪每一个流，根据「原 ip 和 端口号，目标 ip 和 端口号」计算出一个 Stream index 用于唯一标识一个数据流

如果不知道过滤器怎么写，在任意条数据上右键，选择作为过滤器应用即可

### Flow Graph

Statistics -> Flow Graph

![20250608122918](https://image.zuoright.com/20250608122918.png)

## 协议分析

参考视频：<https://youtu.be/0uZfyduC0A0>

### ARP

![20250607191135](https://image.zuoright.com/20250607191135.png)

### TCP

![20250608125414](https://image.zuoright.com/20250608125414.png)

### TLS

![20250607202857](https://image.zuoright.com/20250607202857.png)

四次握手之后，数据被会话密钥（Session key）加密，Wireshark 如果想解密消息则需要拿到客户端生成的预主密钥（Premaster secret），然后推断出会话密钥

通过配置环境变量，让浏览器将预主密钥写入到日志文件：`SSLKEYLOGFILE`

```shell
# Safari 不支持直接导出密钥，需使用 Chromium 或 Firefox
export SSLKEYLOGFILE="/tmp/sslkeylog.log"  # 务必妥善保管，测试后记得将文件删除
open -a "Microsoft Edge" --args --ssl-key-log-file=$SSLKEYLOGFILE  # 或者 Google Chrome
```

然后将预主密钥文件导入 Wireshark：`首选项 -> 协议 -> TLS`

最后抓取的 HTTPs 协议数据即可被解析

### HTTPs

- 请求

![20250607210242](https://image.zuoright.com/20250607210242.png)

- 返回

![20250607214139](https://image.zuoright.com/20250607214139.png)

## 抓包 iPhone

将手机连接电脑，当手机上弹出如下图的提示框，选择信任

> 如果 Mac 没有弹出信任提示框，可在：`Finder(访达)-位置-「设备名」`中点一下信任，然后查看手机

![20210809180340](https://image.zuoright.com/20210809180340.png)

然后打开终端，借助 rvictl 工具开启虚拟网卡，开启后可在 WireShark 中看到虚拟网卡「rvi0」，双击即可抓包

> rvictl, Remote Virtual Interface，是 Xcode 带的一个工具，可以在 macOS 上为连接的 iOS 设备创建一个虚拟网络接口

```shell
rvictl -s udid  # 开启虚拟网卡：rvi0
rvictl -l  # 查看当前开启的设备
rvictl -x udid  # 关闭
```
