# SSH

> <https://wangdoc.com/ssh/>

早期远程命令行服务（Telnet）是明文传输的，于是改用了更安全的SSH(Secure Shell)

- SSH 1 有漏洞
- SSH 2 商业软件
- OpenSSH(OpenBSD Secure Shell) 开源

OpenSSH 还提供一些辅助工具软件（比如 ssh-keygen 、ssh-agent）和专门的客户端工具（比如 scp 和 sftp）

## OpenSSH

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

### 快捷登录

```shell
vim ~/.ssh/config
<<"COMMENT"
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
COMMENT

# 快捷登录
ssh test
# 等同于
ssh -p 7777 demo@remote.example.com
```

### 免密登录

```shell
# 生成公钥：id_rsa.pub
ssh-keygen -t rsa
# 查看公钥
cat ~/.shh/id_rsa.pub
# 将公钥文件传输到服务器
cat ~/.ssh/id_rsa.pub | ssh user@host "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

- 密钥登陆

`ssh -i 密钥.pem root@ip`

### 解决连接不稳定

```shell
# 客户端：Mac
sudo vim /etc/ssh/ssh_config
"""
Host *
    SendEnv LANG LC_*
# 添加以下两行
ServerAliveInterval 30
ServerAliveCountMax 2
"""

# 服务端：Ubuntu
sudo vim /etc/ssh/sshd_config
"""
# 去掉以下两行注释并修改参数
ClientAliveInterval 30  # 间隔多少30s发送一次心跳数据
ClientAliveCountMax 1800  # 30min没反应断掉连接
"""
sudo systemctl restart sshd
```

## SCP

```shell
scp [-P 22] [-p] <~/.ssh/id_rsa.pub> <user@host:~/.ssh/>
<<"COMMENT"
-P 指定端口
-p 保留原文件的修改时间，访问时间和访问权限
COMMENT
```

## Fabric

版本

- Fabric1：旧版本，只支持Python2
- Fabric2：官方版本同时支持Python2和3
- Fabric3：非官方支持Python2和3版本

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
