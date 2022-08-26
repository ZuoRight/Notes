# 引言

- CA：证书发行商
- 系统证书：系统内置的（需要有Root权限才能进行添加或删除）
- 用户证书：用户自行安装的

## 原理

1. 抓包工具先伪装成服务端接收客户端的请求
2. 然后再伪装成客户端将请求发送给真正的服务端

抓包需要伪造公钥证书和私钥，由工具内置的CA证书创建，公钥证书可以在SSL/TLS握手的时候发送给客户端，客户端会对此证书进行验证，为了通过验证，我们需要将证书手动安装在系统中，作为真正的CA。

<https://github.com/youngwind/blog/issues/108>

## 劫持/抓包

各大CA机构的公钥是默认安装在操作系统里的，所以不要安装来路不明的操作系统，否则相当于裸奔。
抓包工具正是利用了这一点，比如先把charles的根证书安装到操作系统。

然后由于服务端没有对客户端做认证，所以Charles可以模拟客户端，获取到服务端公钥（用于后面加密随机数），然后自制了一个证书（charles根证书签名，包含的是Charles自己的公钥），发送给了客户端，而客户端是用提前装好的charles根证书做认证，可以通过，于是客户端用Charles的公钥加密生成密钥（随机数）发送，然后Charles截获，用Charles自己的私钥解密，然后再用先前截获的服务器公钥加密，发给服务端。

## 工具

HTTP协议抓包工具

- Charles
- Fiddler
- Proxyman
- Whistle
- Packet Caputure（安卓专用）
- Stream、Thor（iOS专用）
- mitmproxy 集成定制，测开必备

TCP/IP等底层协议抓包工具

- tcpdump
- WireShark
- tshark 是WireShark下的一个分支，主要用于命令行环境进行抓包、分析，尤其对协议深层解析时，tcpdump难以胜任的场景中
- termshark tshark的命令行UI版本
- pyshark tshark的Python版本

其它

- burpsuite 渗透测试工具，黑客必备

## 反抓包

### SSL-Pinning

1. 客户端内置证书，即在客户端内置仅接受指定域名的根证书，而不接受操作系统或浏览器内置的CA根证书对应的任何证书
2. 客户端内置公钥

### 突破SSL-Pinning

<https://zhuanlan.zhihu.com/p/60392573>

内置证书或者公钥的时候，需要对比验证客户端和服务端的证书或公钥，一般是通用的API，所以可以直接控制这个API的返回结果让验证通过。

1. Xposed+justTrustme：不行？
2. apk反编译工具
3. 使用逆向神器Frida：太难？

<https://blog.csdn.net/xiaohua_de/article/details/80259920>

使用模拟器（首选逍遥模拟器），模拟器不行，使用真机，需要root，root工具（首选刷机精灵）
