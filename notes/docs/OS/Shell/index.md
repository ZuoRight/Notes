# 引言

Shell被称之为用户与内核交互的入口

## Shell

Shell 有以下几种释义

- CLI(Command Line Interface) 命令行交互，通常指的是这个
- Script 命令解释器，即脚本
- 工具箱，提供各种小工具方便使用操作系统的功能

常见的 Shell 有

- Bourne Shell（sh）
- Bourne Again shell（bash）
- C Shell（csh）
- TENEX C Shell（tcsh）
- Korn shell（ksh）
- Z Shell（zsh）
- Friendly Interactive Shell（fish）

最常用的 Shell 当属 Bash，它是 GNU 社区对 Bourne shell 的重写，使其符合自由软件协议，其它 Shell 基本都兼容 Bash

### 查看默认shell

```shell
# Ubuntu 默认 Shell 是 dash，性能高，但会导致一些 bash 命令失败
# MacOS 默认 Shell 是 zsh
ls -l /bin/sh
# 也可以用
echo $SHELL

# 查看内置shell
cat /etc/shells

# 切换shell
# 需要输入密码，会弹出询问界面，问是否让dash为默认shell，选择否，将会删除dash，默认回到bash
sudo dpkg-reconfigure dash
```

### 设置默认SHELL

> 参考译文：<https://juejin.im/post/6844903972294262791>

Mac 系统从 macOS Catalina 版开始默认 Shell 为 zsh，可以切换为bash

但由于GPLv3的原因，Mac自带的 bash 还是十几年前的 3.2 版本，所以需要先升级一下，即下载新版 bash 并设为默认

> tips：编写shell脚本如果想用新bash解释，需要把 `#!/bin/bash` 改为 `#!/usr/local/bin/bash`  

```shell
bash --version  # 查看当前交互shell版本
echo $BASH_VERSION  # 查看默认交互shell版本

# 安装新版
brew install bash

# 查看bash路径
which -a bash
# 新版：/usr/local/bin/bash
# 旧版：/bin/bash，为了保证系统的完整性，不建议删除

# 设置交互shell为新版
chsh -s /usr/local/bin/bash
# 改回旧版bash：chsh -s /bin/bash
# 改回zsh：chsh -s /bin/zsh

# 设置登录shell为新版
# 即把/usr/local/bin/bash填加到当前系统受信任shells配置文件中，
sudo vim /etc/shells  # 需要管理者权限
```

## 工具箱

```shell
# 查看时间
date -R  # Fri, 03 Feb 2019 00:57:49 +0800（注释：+东区，-西区，所以此处为东八区）

# 查看日历
cal 3 2019
```
