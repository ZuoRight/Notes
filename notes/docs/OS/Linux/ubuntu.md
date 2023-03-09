# Ubuntu

> <https://ubuntu.com/download>

Ubuntu LTS(长期支持)版本每两年发布一次，一般能提供5年的支持。

- Ubuntu 18.04.6 LTS (Bionic Beaver) 内置Py3.6
- Ubuntu 20.04.5 LTS (Focal Fossa) 内置Py3.8
- Ubuntu 22.04.1 LTS (Jammy Jellyfish) 内置Py3.10

```bash
# 查看发行版信息
uname -a
lsb_release -a
<<"COMMENT"
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 18.04.6 LTS
Release:        18.04
Codename:       bionic
COMMENT
```

## 包管理器

> 包管理器：`apt`（等价于`apt-get`+`apt-cache`+`apt-config`最常用命令集合）
>
> 安装包格式：`.deb`

- 网络安装

![20220712160747](http://image.zuoright.com/20220712160747.png)

```bash
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

- 本地安装

```bash
ls -l /dev/sr0  # 查看块设备
mount /dev/sr0 /mnt  # 建议挂载到空目录/mnt

dpkg -i /mnt/name-xxx.deb  # 安装
dpkg -P name  # 卸载
```

## 源码编译安装

```bash
wget https://xxx.tar.gz  # 下载tarball(压缩包)文件
sudo tar xfvz xxx.tar.gz -C  /usr/local/src  # 解压缩
cd xxx/  # 可以查看安装说明：README或INSTALL

./configure --prefix=/usr/local/openresty  # 自动生成makefile文件
"""
默认会安装到usr/local，安装完不需要配置环境变量
建议安装到指定目录（方便卸载，直接删除目录），需要配置环境变量：export PATH=xxx
"""

make clean  # 清除旧的目标文件
make [-j2]  # 编译，使用2个逻辑CPU
make check  # 检查
make install  # 将编译完成的文件安装到预定的目录中

# 更新
"""
下载patc文件，更新源代码
更新源代码后，需要重新编译：make
"""

# 删除
"""
手动删除，rm -rf 软件目录
"""
```

## wget 下载

> Linux下一种类似与迅雷的下载软件，通过HTTP、HTTPS、FTP三个最常见的TCP/IP协议下载

```bash
wget -c xxx  # -c 支持断点续传
```

## PPA

Ubuntu 提供了一个名为 Launchpad 的平台，使软件开发人员能够创建自己的软件仓库，即 Personal Package Archive（个人软件包存档），终端用户可以将 PPA 仓库添加到 sources.list 文件中，然后便可以使用 apt 来安装

```bash
sudo add-apt-repository ppa:demo/xxx
sudo apt update
sudo apt install xxx
```

## 常用工具

```bash
apt install busybox -y  # 精简工具集

apt install -y git vim curl jq

# 安装openssh-server用于远程登录（Ubuntu桌面版默认不支持）
apt install -y openssh-server
```

- 图形终端
- 字符终端
- 远程终端：SSH、VNC
- 代码终端：Fabric

Linux（桌面版）默认提供了七个终端（六个命令行界面以及一个图形界面，可以使用Alt+F1/.../7来切换），每个终端可以登录不同用户，每个用户每次登录后获取shell的PID都是不同的，据此可以进行一些个性化设置（`~/.bashrc`），由于CPU每秒可以在不同进程之间快速切换，所以多人同时使用系统时是无感知的。

> 如果某个终端因某个进程卡死导致界面不能动，可以切换到另一个终端，用`ps -aux`找出有问题的进程然后kill掉，再切回刚才的终端就又恢复了正常。

## 命令行

```bash
# 用户名@主机名:所在目录$/# 命令/可执行文件 选项 参数
root@localhost:~$ command [--options] parameter1 parameter2
<<"COMMENT"
系统管理员和普通用户的提示字符分别为：`#` 和 `$`  
选项名前通常用`-` 和 `--`表示简写和全写，也会有带+的情况  
指令间用至少一个空格来分隔，大小写敏感  
回车执行命令，`\`可将回车转义为换行输入  
执行多条命令，用分号隔开
COMMENT
```

修改主机名

```bash
sudo vim /etc/hostname
reboot  # 重启后生效
```

快捷键

- `Tab` 命令及选项补全/提示
- `Ctrl`+`c` 中断目前程序
- `Ctrl`+`d` 键盘输入结束EOF(End Of Input)
- `Shift`+`Page Up/Down` 向前/后翻看输出信息，`clear`后则翻不出来

查看默认shell

```bash
ls -l /bin/sh
<<"COMMENT"
/bin/sh -> dash
ubuntu默认shell是dash，性能高，但会导致一些bash命令失败
"""
# 切换成bash
sudo dpkg-reconfigure dash
"""
输入完密码，会弹出询问界面，问是否让dash为默认shell，选择否，将会删除dash，默认回到bash
COMMENT
```

时间与日期

```bash
# 查看时间
date -R
"""
Fri, 03 Feb 2019 00:57:49 +0800  # +东区，-西区，此处为东八区
"""
# 查看日历
cal 3 2019
```
