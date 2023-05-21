# 引言

- HTTP/1.1
  > Port：80
- HTTPS
  > Port：443
  >
  > 安全：使用 TLS
- HTTP2/SPDY
  > Port：沿用 HTTP/HTTPS
  >
  > 安全：强制使用TLSv1.2
  >
  > 效率：多路复用，头部使用了HPACK压缩算法
- HTTP3/QUIC
  > Port：没有指定默认端口号
  >
  > 安全：强制使用TLSv1.3
  >
  > 效率：传输基于QUIC(Quick UDP Internet Connections)

其它

- 文件传输 FTP
- 电子邮件 E-mail
- 远程登录 Telnet
- 万维网 WWW

## 网络架构模式

主机A的某个进程和主机B上的另一个进程进行通信

- P2P（Peer-to-Peer）不区分客户端和服务端，两台主机是对等关系
- C/S（Client/Server）区分为客户端和服务端，客户端是主机上的程序
- B/S（Browser/Server）区分为客户端端和服务端，客户端是浏览器
