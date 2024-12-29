# WireShark

Wireshark（最早叫做：Ethereal），Wireshark使用WinPCAP作为接口，直接与网卡进行数据报文交换

Mac安装如下图，同时包含了卸载程序，可以阅读文档查看具体安装卸载方法

![20210809131330](https://image.zuoright.com/20210809131330.png)

选择网卡，双击进入抓包页面

![20210809131957](https://image.zuoright.com/20210809131957.png)

在`View/Internals/Supported Protocols`菜单下可以看到WireShark支持的所有协议以及支持的过滤属性等

![20210809165259](https://image.zuoright.com/20210809165259.png)

## 抓包iPhone

将手机连接电脑，当手机上弹出如下图的提示框，选择信任

> 如果Mac没有弹出信任提示框，可在：`Finder(访达)-位置-「设备名」`中点一下信任，然后查看手机

![20210809180340](https://image.zuoright.com/20210809180340.png)

然后打开终端，借助rvictl(Remote Virtual Interface)工具开启虚拟网卡，开启后可在WireShark中看到虚拟网卡「rvi0」，双击即可抓包

> UDID(Unique Device Identifier)，苹果设备的唯一识别码，可以通过：[蒲公英·工具箱扫码获取](https://www.pgyer.com/tools/udid)

```shell
rvictl -s udid  # 开启虚拟网卡：rvi0
rvictl -l  # 查看当前开启的设备
rvictl -x udid  # 关闭
```
