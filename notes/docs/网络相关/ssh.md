# SSH

> <https://wangdoc.com/ssh/>

- `/etc/ssh/ssh_config`  客户端的全局配置文件
- `~/.ssh/config`  用户个人配置文件，优先级高于全局配置文件
- `~/.ssh/id_rsa`  用于SSH协议版本2的RSA私钥
- `~/.ssh/id_rsa.pub`  用于SSH协议版本2的RSA公钥
- `~/.ssh/known_hosts`  包含SSH服务器的公钥指纹

```bash
# ~/.ssh/config
# 服务器1
Host 别名
    HostName IP地址
    Port 22
    User 用户名
    IdentityFile 私钥地址   # 缺省值为~/.ssh/id_rsa
# 服务器2
Host test
    HostName remote.example.com
    Port 7777
    User demo
```

`ssh test` 将等同于 `ssh -p 7777 demo@remote.example.com`

- 免密

```bash
# 把id_rsa.pub里面的内容添加到远程服务器的~/.ssh/目录下的authorized_keys中（如果没有这个文件我们可以创建一个）
ssh-keygen -t rsa
cd ~
mkdir .ssh
cd .ssh/
touch authorized_keys
```
