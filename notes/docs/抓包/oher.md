# 其它工具

## tcpdump

`tcpdump -n -i en0 -s 0 -w demo.pcap host 220.181.38.251`

- `-n`  直接显示IP，而不是把IP转化为域名，速度会快很多
- `-i en0`  指定网卡
- `-s 0`  0表示截取全部报文（默认截取前96字节）
- `-w demo.pcap`  将过滤结果输出到`.pcap`文件，然后借助WireShark等带界面的抓包工具二次分析
- `host ip`  指定IP

![20210809185523](http://image.zuoright.com/20210809185523.png)

参考：[明哥·全网最全tcpdump抓包指南](https://iswbm.com/70.html#9)

## WireShark

Wireshark（最早叫做：Ethereal），Wireshark使用WinPCAP作为接口，直接与网卡进行数据报文交换

Mac安装如下图，同时包含了卸载程序，可以阅读文档查看具体安装卸载方法

![20210809131330](http://image.zuoright.com/20210809131330.png)

选择网卡，双击进入抓包页面

![20210809131957](http://image.zuoright.com/20210809131957.png)

在`View/Internals/Supported Protocols`菜单下可以看到WireShark支持的所有协议以及支持的过滤属性等

![20210809165259](http://image.zuoright.com/20210809165259.png)

### 抓包iPhone

将手机连接电脑，当手机上弹出如下图的提示框，选择信任

> 如果Mac没有弹出信任提示框，可在：`Finder(访达)-位置-「设备名」`中点一下信任，然后查看手机

![20210809180340](http://image.zuoright.com/20210809180340.png)

然后打开终端，借助rvictl(Remote Virtual Interface)工具开启虚拟网卡，开启后可在WireShark中看到虚拟网卡「rvi0」，双击即可抓包

> UDID(Unique Device Identifier)，苹果设备的唯一识别码，可以通过：[蒲公英·工具箱扫码获取](https://www.pgyer.com/tools/udid)

```shell
rvictl -s udid  # 开启虚拟网卡：rvi0
rvictl -l  # 查看当前开启的设备
rvictl -x udid  # 关闭
```

## Postman

- form-data

HTTP请求中的multipart/form-data，既可以上传文件等二进制数据，也可以上传表单键值对，只是最后会转化为一条信息

- x-www-form-urlencoded

只能上传键值对，并且键值对都是间隔分开的

- raw

可以上传任意格式的文本：text、json、xml、html

- binary

只可以上传二进制数据，通常用来上传文件，一次只能上传一个文件

## Jmeter

[官网介绍](https://jmeter.apache.org){ .md-button .md-button--primary }

![20211124153427](http://image.zuoright.com/20211124153427.png)

解压后可以命令行切换到`apache-jmeter-5.4.1/bin`路径下，使用`sh jmeter`命令启动，但为了方便，可以配置环境变量在任意路径下打开，以下是Mac系统的环境变量配置

```shell
# Java
# Jmeter
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_311.jdk/Contents/Home
export JMETER_HOME=/Users/7c/apache-jmeter-5.4.1
export PATH=$PATH:$JAVA_HOME/bin:$PATH:.:$JMETER_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JMETER_HOME/lib/ext/ApacheJMeter_core.jar:$JMETER_HOME/lib/jorphan.jar
```

![20211124154556](http://image.zuoright.com/20211124154556.png)

> 更改默认语言为中文，编辑`apache-jmeter-5.4.1/bin/jmeter.properties`文件，将`#language=en`改为`language=zh_CN`即可

!!! Tips
    如果启动后有闪退或者报错等问题，大概率跟jdk版本有关，我在`jdk-11.0.11`及以上版本都有遇到过各种问题，然后降低到`jdk1.8.0_311.jdk`版本后就稳定多了，哎。
