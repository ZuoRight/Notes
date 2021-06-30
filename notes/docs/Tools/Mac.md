# Mac

## 常用快捷键

- 显示隐藏文件：`Cmd + Shift + .`
- 切换半角和圆角：`Shift + Space`

## 终端

```shell
ls  # 加-f可查看隐藏文件

which xx  # 查找

unzip xxx.zip  # 解压
```

## 设置环境变量

```shell
echo $PATH  # 查看
export PATH=$PATH:xxx >> ~/.bash_profile  # 新增，也可以直接编辑这个文件
source ~/.bash_profile  # 重新加载
```

环境变量文件按加载顺序依次为

```bash
/etc/profile
/etc/paths
~/.bash_profile  # 有这个文件后面的就不读了
~/.bash_login
~/.profile
~/.bashrc
```

## 设置默认SHELL

查看默认shell：`echo $SHELL`

Mac系统从macOS Catalina版开始默认shell为zsh，可以切换为bash，但由于GPLv3的原因，Mac自带的bash还是十几年前的3.2版本，所以需要先升级一下，即下载新版bash并设为默认

```bash
# 安装新版
brew install bash

# 设置交互shell为新版
chsh -s /usr/local/bin/bash
  # 改回旧版：chsh -s / bin / bash
  # 改回zsh：chsh -s / bin / zsh

# 设置登录shell为新版
# 即把/usr/local/bin/bash填加到/etc/shells，需要管理者权限
sudo vim /etc/shells
```

- 查看bash路径：`which -a bash`
  - 新版：`/usr/local/bin/bash`
  - 旧版：`/bin/bash`
- 查看当前交互shell版本：`bash --version`
- 查看默认交互shell版本：`echo $BASH_VERSION`

> tips：
>
> 低旧版本的bash依然存在于`bin/bash`下，为了保证系统的完整性，不建议删除。
>
> 编写shell脚本如果想用新bash解释，记得要把`#!/bin/bash`要改为`#!/usr/local/bin/bash`
>
> 参考译文：<https://juejin.im/post/6844903972294262791>

## [Homebrew](https://brew.sh)

> Mac下包和服务的管理器
>
> 参考文章
>
> - <https://www.cnblogs.com/joyce33/p/13376752.html>  
> - <https://www.xiebruce.top/720.html>

### 安装方法

- 官网安装方法：`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"`
- 镜像安装方法：`/usr/bin/ruby -e "$(curl -fsSL https://cdn.jsdelivr.net/gh/ineo6/homebrew-install/install)"`

不出意外，官网安装方法需要科学上网全局代理才有可能安装成功，所以直接建议用中科大镜像安装方法

Homebrew会被安装到`/usr/local/Homebrew/`路径下

### 替换下载源

可以用`brew config`查看当前配置

安装完Homebrew后，如果想用brew去下载安装其它软件，还需要把下载源替换成国内源，比如[中科大的](http://mirrors.ustc.edu.cn/help/index.html)。

Homebre安装的软件的都在`/usr/local/Cellar/`下，并自动在`/usr/local/bin/`路径下创建link，其中Cellar译为酒窖，而Homebrew官方预先编译好的软件叫做Bottle(酒瓶子)，Bottles就是很多酒瓶子(即软件)，Homebrew把安装一个软件叫做把一个酒瓶子pour(倒入)到酒窖里。

```bash
# brew.git源，用于更新brew本身
`git -C "$(brew --repo)" remote set-url origin https://mirrors.ustc.edu.cn/brew.git`
# 如果想换回官方源，可以
`git -C "$(brew --repo)" remote set-url origin https://github.com/Homebrew/brew.git`


# Formula源，用于brew下载安装软件的库，以前是和brew库在一起，但后来分开了
# 其中homebrew-core，主要安装一些开发者用的无界面程序，由官方维护，此外还有一些第三方维护的库
git -C "$(brew --repo homebrew/core)" remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
# homebrew-cask，主要安装一些AppStore中没有的GUI程序，安装命令为：brew cask install
git -C "$(brew --repo homebrew/cask)" remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git


# homebrew-bottles，Formula源存储的只是软件的名称和下载地址等，真正要去下载预编译二进制软件包是在homebrew-bottles源
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile


# 如果要卸载软件，可能还需要替换掉uninstall脚本
/usr/bin/ruby -e "$(curl -fsSL https://cdn.jsdelivr.net/gh/ineo6/homebrew-install/uninstall)"
```

### 常用命令

```bash
# 查看帮助
brew help

# 查看配置
brew config

# 更新brew本身
brew update

# 查看有哪些软件下载源
brew tap

# 搜索想要安装的软件
brew search xxx

# 查看想安装或已安装软件的信息
brew info xxx

# 安装
brew install xxx
```

```bash
# 查看已安装软件列表
brew list

# 查看已安装软件列表，显示为依赖树的形式
brew deps --installed --tree

# 查看哪些软件可更新，先更新brew
brew outdated
# 更新全部可更新软件
brew upgrade
#  更新xxx
brew upgrade xxx

# 清理旧版本和缓存
brew cleanup

# 卸载
brew uninstall xxx
```

```bash
# 查看安装的服务列表
brew services list

# 大部分需要在后台运行的软件服务，都可以使用以下方式启动/启动并加到开机启动/停止/重启
brew services run/start/stop/restart xxx
```

### 图形管理工具

- [Cakebrew](https://www.cakebrew.com/)

管理Homebrew安装的软件，安装：`brew cask install cakebrew`

- LaunchRocket

管理Homebrew安装的服务，安装：`brew cask install launchrocket`

安装完成后，入口会显示在系统偏好设置页面左下角处，点击即可启动。

## 其它

SIP(系统完整性保护)安全机制：即使是以root身份也无法删除某些程序，比如`/bin`下的，尽管你可以SIP禁用后删除，但也不建议这样去做。
