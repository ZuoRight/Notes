# Git

- 官网：<https://git-scm.com/>
- 官方文档：<https://git-scm.com/book/zh/v2>

## 安装

- Mac：推荐`brew install git`方式安装，如果安装了Xcode会默认自带
- Windows：官网下载`Git for Windows`客户端安装即可
- CentOS：`sudo yum install git`

### 常用命令

- 查看版本：`git --version`
- 查看状态：`git status`

## 基础配置

- 查看当前配置：`git config --list --global`

- 配置代码提交者信息

```shell
# --local 仅对当前用户、当前仓库有效（默认）
# --global 对当前用户、所有仓库有效（一般用这个即可）
# --system 对所有用户、所有仓库有效
git config --global user.name "chonge"
git config --global user.email "chonge@zuoright.com"
```

- 配置SSH密钥

向远程仓库提交代码时需要验证身份，配置SSH密钥，这样就不用每次都输入账户密码验证了

```shell
ssh-keygen  # 生成密钥
cat ~/.shh/id_rsa.pub  # 查看公钥，将公钥添加到GitHub/Gitlab等仓库设置中
```

## 创建项目

- Fork

先Fork别人的项目到自己的远程仓库，然后从自己的远程仓库克隆到本地

- 克隆

```shell
git clone git@github.com:zuoright/demo.git
```

- 本地新建

``` shell
# 先在本地创建一个仓库
mkdir demo
cd demo
# 然后初始化，会生成.git跟踪文件
git init
```

- 远程新建

```shell
# 与本地仓库关联，origin为远程库名称，可自定义
git remote add origin git@github.com:zuoright/demo.git
# 查看关联了哪些远程库
git remote -v
# 重命名远程库
git remote rename old_name new_name
# 删除已关联的远程库demo
git remote rm demo
```

## 完善项目

- 创建 `LICENSE` 文件：编写授权声明
- 创建 `README.md` 文件：编写项目介绍
- 创建 `.gitignore` 文件：编写忽略规则

> 对于那些已经被track(追踪)的文件，需要先删除相应的跟踪记录：
> 
> `git rm -r --cached 要忽略的文件路径`
> 
> [各语言忽略规则通用模版](https://github.com/github/gitignore)

## 提交代码

```shell
# 提交代码
git add .
git commit -m "init"

# 推送到远程仓库origin的main分支
# 可以加-u参数设置默认推送的仓库和分支，下次直接git push即可
git branch -M main  # 将本地主分支设置为main
git push [-u] origin main
```
