# Mac

## 常用快捷键

- 显示隐藏文件：`Cmd + Shift + .`
- 切换半角和圆角：`Shift + Space`

## Mac环境变量

### 加载顺序

系统变量：

1. `/etc/profile`
2. `/etc/paths`

用户变量：

3. `~/.bash_profile`  (Linux下是`~/.bashrc`)
4. `~/.bash_login`
5. `~/.profile`

### 设置PATH

```bash
# 查看
echo $PATH
# 添加
echo 'export PATH=$PATH:xxx' >> ~/.bash_profile
# 应用
source ~/.bash_profile
```

## [Homebrew](https://brew.sh)

> Mac下包和服务的管理器
> 
> 参考文章
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

## 默认Shell，以及升级Bash

> 从macOS Catalina版开始，Mac将使用zsh作为默认登录Shell和交互式Shell，但依然可以使用bash作为默认Shell，不过由于GPLv3的原因，Mac自带的bash还是十几年前的3.2版本，所以如果你想用bash作为默认Shell，很有必要升级一下子。
> 
> 参考译文：<https://juejin.im/post/6844903972294262791>

建议使用Homebrew安装最新版本：`brew install bash`，成功后会在`/usr/local/bin`路径下生成新bash的link。

此时使用`which -a bash`显示系统上存在两个版本的bash，查看系统环境变量`/etc/paths`可知：`/usr/local/bin/bash`会优先于`/bin/bash`加载，所以此时在Shell下输入`bash --version`返回的是新bash的版本。

但此时`echo $BASH_VERSION`发现默认交互式Shell依然还是旧版本，需要`chsh -s /usr/local/bin/bash`设置为新bash。

理论上将新bash设置为默认交互Shell后，默认登录Shell也应自动变为新bash，但`cat /etc/shells`发现受信任Shell列表中没有新bash，意味着它不允许被用作默认登录Shell，所以需要`sudo vim /etc/shells`以root身份将新bash加入到白名单列表中。

需要注意的是，编写shell脚本如果想用新bash解释，记得要把`#!/bin/bash`要改为`#!/usr/local/bin/bash`

一点说明：所谓的升级新bash，其实是下载了一个新版本的bash作为默认bash，而旧版本的bash依然存在于`bin/bash`下，但为了保证系统的完整性，即使是以root身份也无法删除某些程序，比如`/bin`下的，尽管你可以禁用SIP(系统完整性保护)安全机制后删除，但也不建议这样去做。
