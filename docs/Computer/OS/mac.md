# Mac

```shell
lsof -i:3031  # 查看端口号是否占用
kill [-9] 3031  # 杀死进程，-9强制杀死
launchctl  # 类似Linux的systemctl
which xx  # 查找
unzip xxx.zip  # 解压
env  # 查看环境变量，比如path变量以及自定义的临时变量等

# 清理 DNS 缓存
sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder
```

## Xcode

从 Mac App Store 下载

官方文档：<https://developer.apple.com/documentation/xcode/>

截图 iPhone：Window -> Devices and Simulators（需要手机先开启「设置 -> 隐私与安全 -> 开发者模式」）

## CLT

[介绍文档](https://developer.apple.com/library/archive/technotes/tn2339/_index.html)

Command Line Tools，是可独立 Xcode 下载安装的小型工具包，包含了 MacOS SDK 和一些常用的命令行工具，比如 Git、clang 等

安装 Homebrew 等工具会提示先安装 CLT，比如执行 `git --version` 的时候会弹出安装 CLT 的窗口

有时 MacOS 更新可能会遇到 CLT 丢失的问题：`xcrun: error: invalid active developer path ..., missing xcrun at: ...`，导致 Git 等不能用，根据提示手动安装 CTL：`xcode-select --install` 即可

## Homebrew

Mac 系统下的一款开源包管理器，[项目地址](https://github.com/Homebrew)，[官网](https://brew.sh)

由四部分组成

- Homebrew 源代码仓库
- Homebrew-core 核心源
- Homebrew-cask 提供 MacOS 应用和大型二进制文件的安装
- Homebrew-bottles 预编译二进制软件包

Homebrew 官方把预先编译好的软件叫做 Bottle(酒瓶子)，把酒瓶子倒入（pour）酒窖（Cellar），即安装软件。

```shell
# 官方下载（很慢，建议开全局代理）
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
# 镜像下载
/usr/bin/ruby -e "$(curl -fsSL https://cdn.jsdelivr.net/gh/ineo6/homebrew-install/install)"

"""
M1安装完后会提示配置环境变量
==> Next steps:
- Run these two commands in your terminal to add Homebrew to your PATH:
    echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/chonge/.bash_profile
    eval "$(/opt/homebrew/bin/brew shellenv)"
"""

# 卸载，卸载不干净再装会报错
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"
```

查看 Homebrew 的安装路径

```shell
brew --prefix
`
Intel 处理器:
    /usr/local/Homebrew/
    /usr/local/Cellar/

Apple 处理器:
    /opt/homebrew
    /opt/homebrew/Cellar 并自动在 /usr/local/bin/ 路径下创建 link
`

# 打印 Python3 的绝对路径
echo $(brew --prefix)/bin/python3
```

### 更换下载源

> 参考：<https://www.xiebruce.top/720.html>

```shell
brew tap mongodb/brew  # 添加下载源
brew tap  # 查看有哪些下载源
`
homebrew/services
mongodb/brew
`
```

```shell
# 更换brew.git源，主要用于更新brew本身
# 切换到Homebre源路径：/usr/local/Homebrew/
cd $(brew --repo)  # 等同于：git -C "$(brew --repo)"
git remote -v  # 查看远程库信息
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git  # 换成镜像源
git remote set-url origin https://github.com/Homebrew/brew.git  # 换回官方源


# Formula源，用于brew下载安装软件的库，以前是和brew库在一起，但后来分开了
# 更换homebrew-core，主要安装一些开发者用的无界面程序，由官方维护，此外还有一些第三方维护的库
# 切换到 /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core
cd $(brew --repo homebrew/core)
git remote -v  # 查看远程库信息
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git  # 换成镜像源
git remote set-url origin https://github.com/Homebrew/homebrew-core.git  # 换回官方源


# 设置 homebrew-bottles 环境变量
# Formula源存储的只是软件的名称和下载地址等，真正要去下载预编译二进制软件包是在homebrew-bottles源
# 如果要恢复官方源，只需要删除或注释掉这个环境变量即可
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile


# 更换homebrew-cask源(可选)，主要安装一些AppStore中没有的GUI程序
# 切换到：/usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask
cd $(brew --repo homebrew/cask)
git remote -v  # 查看远程库信息
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git  # 换成镜像源
git remote set-url origin https://github.com/Homebrew/homebrew-cask.git  # 换回官方源
# homebrew-cask扩展homebrew-cask-versions(可选)
cd $(brew --repo)/Library/Taps/homebrew/homebrew-cask-versions
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask-versions.git  # 换成镜像源
git remote set-url origin https://github.com/Homebrew/homebrew-cask-versions.git  # 换回官方源
```

### 常用命令

```shell
brew help  # 查看帮助
brew config  # 查看配置
brew doctor  # 检查问题
```

```shell
brew search xxx  # 搜索想要安装的软件
brew info xxx  # 查看想安装或已安装软件的信息

brew install xxx  # 安装，默认会先检查升级brew本身，可以Ctrl+c跳过
brew uninstall xxx  # 卸载
brew autoremove  # 删除依赖项
brew cleanup  # 清理旧版本和缓存

brew list [--casks] # 查看已安装软件列表
brew deps --installed --tree  # 查看已安装软件列表，显示为依赖树的形式
brew leaves  # 列出未被依赖的包
brew outdated  # 查看哪些软件可更新，先更新brew

brew update  # 更新homebrew和Formulae
brew upgrade  # 更新全部可更新软件
brew upgrade xxx  # 更新xxx
```

```shell
# 查看安装的服务列表
brew services list
"""
Name     Status  User File
rabbitmq none         
redis    started      /opt/homebrew/opt/redis/homebrew.mxcl.redis.plist
"""

# 大部分需要在后台运行的软件服务，都可以使用以下方式启动/启动并加到开机启动/停止/重启
brew services run/start/stop/restart xxx

brew services info redis
"""
redis (homebrew.mxcl.redis)
Running: ✔
Loaded: ✔
Schedulable: ✘
User: 
PID: 43685
"""
```

## 其它

### 快捷键

- 显示隐藏文件：`Cmd + Shift + .`
- 切换半角和圆角：`Shift + Space`
- 清空命令行：`Cmd + k`

### 开启三指拖移

![20220305004724](https://image.zuoright.com/20220305004724.png)

### SIP

SIP(系统完整性保护)安全机制：即使是以root身份也无法删除某些程序，比如 `/bin` 下的，尽管你可以SIP禁用后删除，但也不建议这样去做。

### 测试网速

- 命令行

```shell
networkQuality
'
==== SUMMARY ====
Uplink capacity: 946.545 Kbps
Downlink capacity: 5.069 Mbps
Responsiveness: Low (7.500 seconds | 8 RPM)
Idle Latency: 75.875 milliseconds | 800 RPM
'
```

- 第三方工具：Speedtest
