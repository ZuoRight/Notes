# Linux 发行版

我们平时常用的 Linux 其实指的 Linux 发行版（distribution），它是在 Kernel 的基础上定制化扩展了很多服务和功能。

发行版本千千万，不是红帽就大便，意思就是 Linux 的发行版有很多，但主要分两个派系：Redhat 和 Debian

> 发行版列表：<https://zh.wikipedia.org/wiki/Linux%E5%8F%91%E8%A1%8C%E7%89%88>

![20220707151705](https://image.zuoright.com/20220707151705.png)

> 发行版选择器：<https://distrochooser.de/>

比较常见的是

- Ubuntu
- CentOS
- Alpine 容器中可能比较常用

```shell
# 查看发行版信息
lsb_release -a
'
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 18.04.6 LTS
Release:        18.04
Codename:       bionic
'

apt或yum install kernel-xxx  # 升级内核
apt或yum update  # 更新已安装的其它软件包和补丁

su - username  # 用户登录

reboot  # 重启
shutdown  # 关机
```

## Ubuntu

> <https://ubuntu.com/download>

Ubuntu LTS(长期支持)版本每两年发布一次，一般能提供 5 年的支持。

- Ubuntu 18.04.6 LTS (Bionic Beaver) 内置Py3.6
- Ubuntu 20.04.5 LTS (Focal Fossa) 内置Py3.8
- Ubuntu 22.04.1 LTS (Jammy Jellyfish) 内置Py3.10

### 包管理器安装

- 官方仓库
- 个人仓库 PPA, Personal Package Archive

Ubuntu 提供了一个名为 Launchpad 的平台，使软件开发人员能够创建自己的软件仓库

```shell
sudo add-apt-repository ppa:demo/xxx  # 将 PPA 仓库添加到 sources.list 文件中
sudo apt update  # 然后便可以使用 apt 来安装 PPA 中的软件
sudo apt install xxx
```

包管理器 `apt`，安装包格式：`.deb`

> 等价于旧命令集合 `apt-get` + `apt-cache` + `apt-config`

![20220712160747](https://image.zuoright.com/20220712160747.png)

```shell
apt update
apt list --installed  # 已安装
apt list --upgradable  # 可升级
apt upgrade  # 升级所有可升级的软件包

apt install xxx
"""
-y：yes，在命令行交互提示中，直接输入 yes
-f：fix解决包依赖的问题
--no-install-recommends 参数来避免安装非必须的文件，从而减小镜像的体积

- 下载的软件的存放位置：/var/cache/apt/archives
- 安装后软件的默认位置：/usr/share
- 可执行文件位置：/usr/bin
- 配置文件位置：/etc
- lib文件位置：/usr/lib
"""
```

### 本地安装

```shell
ls -l /dev/sr0  # 查看块设备
mount /dev/sr0 /mnt  # 建议挂载到空目录/mnt

dpkg -i /mnt/name-xxx.deb  # 安装
dpkg -P name  # 卸载
```

## CentOS

### 包管理器安装

官方库：`/etc/yum.repos.d/CentOS-Base.repo`

包管理器：`yum`（Yellow dog Updater, Modified）

安装包格式：`name-软件版本-系统版本.平台.rpm`

```shell
yum search xxx
yum list xxx
yum install xxx
yum remove xxx
yum update xxx
```

### 本地安装

官网找 `·.rpm` 安装包，然后挂载到空目录

```shell
ls -l /dev/sr0  # 查看块设备
mount /dev/sr0 /mnt  # 建议挂载到空目录/mnt

rpm -i /mnt/name-xxx.rpm  # 安装install
rpm -qa | more  # 查询所有已安装的软件包
rpm -qi name  # 查询指定安装包信息
rpm -e name  # 卸载
```

## 用户操作

- 切换用户

```shell
# 将身份切换为其他用户
su - [username]  # 非root用户切换时需要输入对方密码，使用exit可以退回原来的用户
<<"COMMENT"
不加username默认切换到root用户
带 - 表示完全切换，即当前路径切换为对方家目录，否则还会留在当前用户的家目录
如果切换失败可以试试：sudo bash
COMMENT

# 以其他用户的身份执行命令
sudo [-u username] command
<<"COMMENT"
需要其它用户授权给你，才可以使用sudo
    可以用visudo命令（vi只有读取权限）编辑/etc/sudoers文件，给用户添加一行与root相同的权限即可

不带usernamem默认切换root用户
加-s可以保持session
COMMENT
```

> 用户相关的很多操作都需要root用户权限

```shell
groupadd group1  # 创建用户组
groupdel group1  # 删除用户组

# 创建用户
useradd [-g group_name] username
<<"COMMENT"
-g 指定用户组，否则默认会创建并指定一个与用户同名的用户组
-m 强制建立家目录(一般默认)
-s 指定默认shell
COMMENT

# 删除用户
userdel [-r] username
<<"COMMENT"
防止误删，删除后用户的家目录还会保留在/home中
    -r 连同家目录一起删除
COMMENT

# 修改用户信息
usermod username
<<"COMMENT"
usermod -g group_new username  修改用户组
usermod -d /home/new_path username  修改家目录存放位置
usermod -s /bin/bash username  修改默认shell
COMMENT

# 设置/修改密码
passwd [username]
<<"COMMENT"
密码为空则不需要密码即可登录
root用户可以修改任意用户的密码且不需要输入旧密码
不加username则默认修改当前用户的密码
COMMENT
```

> 每个登录的用户都会获得两个ID：`UID`(User ID) 和 `GID`(Group ID)，root的ID都是0

```shell
# 查看某个用户的账号信息，不带用户名默认查看自己
# 用户不存在则返回no such user，可以用来判断用户是否存在
id [username]
# 查看所属用户组
groups
# 查看当前在线用户
who  # 或者 w
```

### 用户配置文件

- `/etc/passwd`

账号信息存储在`/etc/passwd`文件中，其中很多都是系统账号（系统运行所必须的），不可随意删除

> 其中`/usr/sbin/nologin`表示不允许登陆
>
> 密码为x，表示需要密码，如果为空表示不需要密码

![20210827231927](https://image.zuoright.com/20210827231927.png)

- `/etc/shadow`

> 早期密码存在`/etc/passwd`中，但后来因为安全原因单独存到了：`/etc/shadow`

![20210827233831](https://image.zuoright.com/20210827233831.png)

> 最近修改日期：是从1(1970.1.1)开始累计的，多少天内不可修改或需要修改，为0则表示可任意修改，99999为273年，即不限制

```shell
usermod -chage username  # 修改失效日期，如果想暂停某个用户登录，可以将失效日期改为0
```

- `/etc/group`

用户组配置文件

### 给用户发消息

```shell
write username  # 给某个在线用户发送消息
wall  # 给所有在线用户发送消息
mesg n/y  # 拒绝或允许接收消息，root用户默认拒绝，普通用户无法拒绝root用户发来的消息
mail -s "test content" [chonge@localhost]  # 发送邮件，也可以给离线用户发，给本机用户可以省略[chonge@localhost]，对方可以使用mail命令来接收查看邮件内容
```

![20210828001620](https://image.zuoright.com/20210828001620.png)

![20210828001628](https://image.zuoright.com/20210828001628.png)
