# 终端

Terminal 终端，是基于文本的输入输出机制，需要引入 Shell 来解释具体的命令及其语法。通过命令行（Command Line）与 Shell 交互。

参考：[What's the difference between a terminal, a console, and a shell?](https://www.hanselman.com/blog/whats-the-difference-between-a-console-a-terminal-and-a-shell)

在计算机早期还是大型机的时代，操作系统由单任务发展为多任务的分时系统(time-sharing)，允许多人同时使用一台机器，于是便引入了终端(terminal)的概念

由系统管理员控制的本地终端被称之为控制台(Console)，由电话网络连入的其它终端被称之为远程终端

![20230330233936](https://image.zuoright.com/20230330233936.png)

## 发展演变

![20230331133529](https://image.zuoright.com/20230331133529.png)

> LDISC(Line DISCipline) 用来撮合底层的硬件驱动与上层的系统调用，并完成某些控制字符的处理与翻译  
> UART 通用异步接收器和发送器，用来处理物理线路的字符传输（比如错误校验和流控等）

### 硬件终端

最初的终端就是指电传打字机(Teletype)，打印机作为输入，打印纸作为输出，后来使用 CRT 显示器替代了打印纸

> tty 是电传打印机英文单词的缩写，所以通常用 tty 表示终端的统称  
> 早期连接终端设备是通过串行端口连接的，所以串口设备也常被称呼为 tty 设备

![Teletype Model 33 ASR](https://image.zuoright.com/20230330233916.png)

![VT05 终端](https://image.zuoright.com/20230330233924.png)

三种缓冲模式

- 字符模式：每按一个键就会立即发送，无缓冲，基本淘汰
- 行模式：按下回车键后发送一整行，用的最多
- 屏模式：缓存当前屏幕内容，支持方向键移动，用的较少

### 虚拟终端

由内核直接提供的虚拟终端 Virtual Console

Linux 内置了 7 个虚拟终端，前 6 个为字符终端(Character Terminal)，用于命令行交互(CLI)，第 7 个预留给了图形终端(Graphical Terminal)，用于 GUI 操作

可以使用快捷键 `[Ctrl] + Alt + F1/2/3/4/5/6/7` 来切换，每个终端可以登录不同用户，每个用户每次登录后获取 Shell 的 PID 都是不同的，据此可以进行一些个性化设置（`~/.bashrc`），由于 CPU 每秒可以在不同进程之间快速切换，所以多人同时使用系统时是无感知的

> 如果某个终端因某个进程卡死导致界面不能动，可以切换到另一个终端，用 `ps aux` 找出有问题的进程然后 kill 掉，再切回刚才的终端就又恢复了正常

```shell
toe -a  # 列出系统支持的终端类型
tty  # 查看当前shell关联到了哪个tty，比如：/dev/ttys010
lsof /dev/ttys010  # 查看tty被哪些进程打开
```

### 终端模拟器

Terminal Emulator 终端模拟器，又叫 Terminal Window

伪终端 Pseudo Terminals, PTY 是终端模拟器的软件接口

我们现在连接远程服务器所使用的 SSH、VNC、Fabric 等客户端/软件库属于远程终端模拟器

## SSH

SSH, Secure Shell，参考：<https://wangdoc.com/ssh/>

> 早期远程命令行服务（Telnet）是明文传输的

- SSH 1 有漏洞
- SSH 2 商业软件
- OpenSSH(OpenBSD Secure Shell) 开源

```shell
# 安装 openssh-server 用于远程登录
apt install -y openssh-server
```

OpenSSH 还提供一些辅助工具（比如 ssh-keygen 、ssh-agent）和专门的客户端工具（比如 scp 和 sftp）

```text
 +----------+       +------------+
 | Keyboard |------>|            |
 +----------+       |  Terminal  |
 | Monitor  |<------|            |
 +----------+       +------------+
                          |
                          |  ssh protocol
                          |
                          ↓
                    +------------+
                    |            |
                    |    sshd    |--------------------------+
                    |            |           fork           |
                    +------------+                          |
                        |   ↑                               |
                        |   |                               |
                  write |   | read                          |
                        |   |                               |
                  +-----|---|-------------------+           |
                  |     |   |                   |           ↓
                  |     ↓   |      +-------+    |       +-------+
                  |   +--------+   | pts/0 |<---------->| shell |
                  |   |        |   +-------+    |       +-------+
                  |   |  ptmx  |<->| pts/1 |<---------->| shell |
                  |   |        |   +-------+    |       +-------+
                  |   +--------+   | pts/2 |<---------->| shell |
                  |                +-------+    |       +-------+
                  |    Kernel                   |
                  +-----------------------------+
```

### 配置路径

- `/etc/ssh/ssh_config` 客户端的全局配置文件
- `~/.ssh/config` 用户个人配置文件，优先级高于全局配置文件
- `~/.ssh/id_rsa` RSA私钥
- `~/.ssh/id_rsa.pub` RSA公钥
- `~/.ssh/known_hosts` 包含SSH服务器的公钥指纹

### 登录

- 密钥登陆

`ssh -i 密钥.pem root@ip`

- 免密登录

首先需要先生成公钥

```shell
# 生成公钥：id_rsa.pub
ssh-keygen -t rsa

# 查看公钥
cat ~/.shh/id_rsa.pub
```

然后将公钥配置到远端服务器中

```shell
# 使用 SCP 工具把公钥文件复制到远端
scp -P 22 -p ~/.ssh/id_rsa.pub user@host:~/.ssh/
'
-P 指定端口
-p 保留原文件的修改时间，访问时间和访问权限
-r 递归复制整个目录
'

# 或者将公钥内容追加到远端的authorized_keys中
cat ~/.ssh/id_rsa.pub | ssh user@host "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

然后便可以免密登录

```shell
ssh -p 7777 demo@remote.example.com

ssh test  # 还可以快捷登录
```

- 快捷方式需要以下配置

```shell
vim ~/.ssh/config
'
# 服务器1
Host 别名
    HostName IP地址
    Port 22
    User 用户名
    IdentityFile 私钥地址   # 缺省值为：~/.ssh/id_rsa
# 服务器2
Host test
    HostName remote.example.com
    Port 7777
    User demo
'
```

### 解决连接不稳定

```shell
# 客户端：Mac
sudo vim /etc/ssh/ssh_config
'
Host *
    SendEnv LANG LC_*
# 添加以下两行
ServerAliveInterval 30
ServerAliveCountMax 2
'

# 服务端：Ubuntu
sudo vim /etc/ssh/sshd_config
'
# 去掉以下两行注释并修改参数
ClientAliveInterval 30  # 间隔多少30s发送一次心跳数据
ClientAliveCountMax 1800  # 30min没反应断掉连接
'
sudo systemctl restart sshd
```

## Fabric

版本

- Fabric1：旧版本，只支持 Python2
- Fabric2：官方版本同时支持 Python2 和 3
- Fabric3：非官方支持 Python2 和 3 版本

```python
import paramiko
import os


class RunShell():
    def __init__(self):
        self.__host = "xxx.xxx.xx.xx"
        self.__port = 22
        self.__username = "root"
        self.__password = "xxxx"

    # 基于用户名和密码的sshclient方式登陆
    def ssh(self, cmd):
        ssh = paramiko.SSHClient()
        # 允许连接不在know_host文件中的主机，此方法必须放在connect方法的前面
        ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
        # 建立连接
        ssh.connect(hostname=self.__host, port=self.__port, username=self.__username, password=self.__password)
        # 使用这个连接执行命令，结果放到stdout中，如果有错误将放到stderr中
        stdin, stdout, stderr = ssh.exec_command(cmd)
        result = stdout.read().decode("utf-8")

        # 关闭连接
        ssh.close()

        return result

    # 上传或下载文件
    def sftp(self, up_or_down, localpath, remotepath):
        # 实例化一个trans对象
        trans = paramiko.Transport((self.__host, self.__port))
        # 建立连接
        trans.connect(username=self.__username, password=self.__password)
        # 实例化一个sftp对象，指定连接的通道
        sftp = paramiko.SFTPClient.from_transport(trans)

        result = "未执行"
        if up_or_down == "up":
            if not os.path.exists(localpath):
                result = "未找到要上传的文件"
            else:
                # 上传文件
                sftp.put(localpath=localpath, remotepath=remotepath)
                self.ssh("systemctl reload nginx")
                result = "上传成功，并已重启服务器"
        elif up_or_down == "down":
            if remotepath == "":
                result = "没有找到服务器中相应的路径"
            else:
                # 下载文件
                sftp.get(remotepath=remotepath, localpath=localpath)
                result = "下载成功"
        else:
            result = "不知道该上传还是下载"

        trans.close()

        return result
```

## Tabby

<https://github.com/eugeny/tabby>

Tabby（以前称为 Terminus）是适用于 Windows、macOS 和 Linux 的高度可配置的终端仿真器、SSH 和串行客户
