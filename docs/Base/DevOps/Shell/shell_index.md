# Shell Index

Shell 被称之为用户与内核交互的入口，有以下几种释义

- CLI（Command Line Interface） 命令行交互环境，通常指的是这个
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

最常用的 Shell 当属 Bash，它是 GNU 社区对 Bourne Shell 的重写，使其符合自由软件协议，其它 Shell 基本都兼容 Bash

## 默认 Shell

Ubuntu 默认 Shell 是 dash，性能高，但会导致一些 bash 命令失败

MacOS 默认 Shell 是 zsh

- 查看安装的所有shell

```shell
cat /etc/shells
'
# List of acceptable shells for chpass(1).
# Ftpd will not allow users to connect who are not using
# one of these shells.

/bin/bash
/bin/csh
/bin/dash
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zsh
/opt/homebrew/bin/bash
'
```

- 查看默认shell

默认 shell 不一定是当前正在使用的 shell，使用 `ps` 命令返回进程列表，倒数第二个一般是当前正在使用的 shell

```shell
echo $SHELL
```

- 切换shell

```shell
# 需要输入密码，会弹出询问界面，问是否让dash为默认shell，选择否，将会删除dash，默认回到bash
sudo dpkg-reconfigure dash
```

## 设置 MacOS 默认 Shell

> 参考译文：<https://juejin.im/post/6844903972294262791>

macOS 从 Catalina 版开始默认 Shell 为 zsh，可以切换为 bash

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

## 快捷键

```text
Tab 命令及选项补全/提示
Shift + PageUp/PageDown 向前/后翻看输出信息，清空后则翻不出来

Ctrl + s 打开/关闭回显（即输入不显示在屏幕上，常见于输入密码时）

Ctrl + c 中断目前程序
Ctrl + d 键盘输入结束EOF(End Of Input)，即关闭shell会话

Ctrl + z 让进程变为后台进程，用 fg 命令切换回前台

Ctrl + w 删除光标前一个单词
Ctrl + u 删除光标前的单词直到行首
Ctrl + k 删除光标后的单词直到行尾
```

## 工具箱

```shell
# 查看时间
date -R  # Fri, 03 Feb 2019 00:57:49 +0800（注释：+东区，-西区，所以此处为东八区）

# 查看日历
cal 3 2019
```
