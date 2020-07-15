mac 设置别名

~/.ssh/config

```text
# 服务器1
Host 别名
    HostName IP地址
    Port 22
    User 用户名
    IdentityFile 私钥地址 可省略 默认为~/.ssh/id_rsa
# 服务器2
Host 别名
    HostName IP地址
    Port 22
    User 用户名
```

免密

ssh-keygen -t rsa

把id_rsa.pub里面的内容添加到远程服务器的~/.ssh/目录下的authorized_keys中（如果没有这个文件我们可以创建一个）

cd ~
mkdir .ssh
cd .ssh/
touch authorized_keys
