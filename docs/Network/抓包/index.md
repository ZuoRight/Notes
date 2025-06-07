---
comments: true
hide:
  - feedback
---

# 引言

抓包工具充当正向代理，客户端需手动配置代理地址将流量发送到代理服务器，在此基础上利用中间人机制（MITM, Man-in-the-Middle），拦截请求或返回，解密 HTTPS 流量然后再抓发

抓包需要伪造公钥证书和私钥，由工具内置的 CA 证书创建，公钥证书可以在 SSL/TLS 握手的时候发送给客户端，客户端会对此证书进行验证，为了通过验证，我们需要将证书手动安装在系统中，作为真正的 CA。

抓包工具（比如 Charles）会要求用户将其根证书安装到操作系统，然后由于服务端没有对客户端做认证，所以 Charles 可以模拟客户端，获取到服务端公钥（用于后面加密随机数），然后自制了一个证书（Charles 根证书签名，包含的是 Charles 自己的公钥），发送给了客户端，而客户端是用提前装好的 Charles 根证书做认证，可以通过，于是客户端用 Charles 的公钥加密生成密钥（随机数）发送，然后 Charles 截获，用 Charles 自己的私钥解密，然后再用先前截获的服务器公钥加密，发给服务端。

## HTTP 协议抓包工具

- Charles
- Fiddler
- Proxyman
- Whistle
- Android: Packet Caputure
- iOS: Stream、Thor

## TCP/IP 等底层协议抓包工具

- tcpdump
- WireShark
- tshark: WireShark 下的一个分支，主要用于命令行环境进行抓包、分析，尤其对协议深层解析时，tcpdump难以胜任的场景中
    - termshark: tshark 的命令行 UI 版本
    - pyshark: tshark 的 Python 版本

## 其它抓包工具

- burpsuite 不仅提供了代理功能，还提供了安全和渗透测试工具
- mitmproxy 集成定制
- Postman 虽然也可以抓包，但并不是其主要功能，所以比较弱
