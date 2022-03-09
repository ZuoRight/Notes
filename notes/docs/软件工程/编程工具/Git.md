# Git

- 官网：<https://git-scm.com/>
- 官方文档：<https://git-scm.com/book/zh/v2>

## VCS

Version Control System 版本控制系统，主要分为集中式和分布式两种。

集中式最常用的是SVN，但如今最流行的VCS无疑是分布式的Git，也就是我们今天的主角。

> 所有的版本控制系统，只能跟踪文本文件的改动，对于二进制文件只能跟踪其大小的变化

- 文本文件：各种源代码、纯文本等
- 二进制文件：图片、视频、office文件(其实是个压缩包，由很多.xml和其他文件构成，所以属于二进制)

## 安装

- Ubuntu：`sudo apt-get install git`
- CentOS：`sudo yum install git`
- Mac：推荐`brew install git`方式安装，如果安装了Xcode会默认自带，此时用brew安装会提示已经存在，根据提示执行`brew link --overwrite git`覆盖
- Windows：官网下载`Git for Windows`客户端安装即可

## 常用命令

- 查看git版本：`git --version`
- 查看当前状态：`git status`
- 查看提交日志：`git log`

## 基础配置

- 查看当前配置：`git config --list --global`

- 配置代码提交者信息

```shell
# --local 仅对当前用户、当前仓库有效（默认）
# --global 对当前用户、所有仓库有效（一般用这个即可）
# --system 对所有用户、所有仓库有效
git config --global user.name "7c"
git config --global user.email "7c@zuoright.com"
```

- 配置SSH密钥

向远程仓库提交代码时需要验证身份，配置SSH密钥，这样就不用每次都输入账户密码验证了

```shell
# 生成密钥
# 回车，会提示保存位置
# 再回车，如果已存在则会提示是否重新生成
# 如果提示输入密钥类型，推荐选择ed25519，没提示一般默认会是rsa类型
ssh-keygen
# 查看公钥
cat ~/.shh/id_rsa.pub
# 将公钥添加到项目托管平台相应设置中
```

## 创建项目

- Fork

先Fork别人的项目到自己的远程仓库，然后从自己的远程仓库克隆到本地

- 克隆

```shell
git clone git@github.com:zuoright/demo.git
# 默认克隆主分支，可加-b xxx指定分支
# 默认把远程仓库命名为origin，可加-o参数自定义
git clone [-b 指定分支] git@github.com:zuoright/demo.git [-o xxx]
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

- `touch LICENSE` 文件：编写授权声明
- `touch README.md` 文件：编写项目介绍
- `touch .gitignore` 文件：编写忽略规则

> [各语言忽略规则通用模版](https://github.com/github/gitignore)
>  
> 对于那些已经被track(追踪)的文件，需要先删除相应的跟踪记录：
>  
> `git rm --cached <file>`

## 提交代码

```shell
# 查看修改了哪些内容（工作区与暂存区的对比，所以文件必须是已追踪过的）
git diff

# 提交代码到暂存区
git add xx  # 提交某个
git add .  # 提交全部，也可用-A或--all参数

# 代码保存到本地仓库
git commit  # 不加-m会打开默认编辑器编辑
git commit -m "init"
git commit -am "xxx"  # 跳过暂存区提交
git commit --ament -m "xxx"  # 修正提交，可以覆盖上一次的提交
```

## 回滚

`git reset [--参数]  版本 [指定文件]`

回滚【到】某个版本，回滚到后，后面的版本都将消失

回滚后要用`git push -f`推向远程，否则会报错

参数

- --soft 回到commit前的状态
- --mixed 回到add前的状态，默认
- --hard 回到工作区修改前状态，重置

版本

- 版本号（后七位即可）
- 标签
- HEAD指针
  - HEAD 当前版本
  - HEAD^ 上一版本
  - HEAD^^ 上上版本
  - HEAD~100 上100个版本

`git revert -n 版本号`

回滚某个版本，生成新的版本，不影响其他版本

## 撤销

撤销工作区未追踪文件的修改：只能是ctrl+Z

撤销工作区已追踪文件的修改

- `git restore xxx`
- `git checkout -- xxx`

撤销暂存区文件缓存：即git add后撤销

- `git restore --staged xxx` 变为未暂存状态
- `git rm --cached xxx` 变成未追踪状态

## 分支

```shell
git branch [-a] # 查看，加-a会显示远程分支(红色标示)
git branch dev  # 新建
git checkout dev  # 切换，git switch dev
git checkout -b dev  # 新建并切换
git merge dev  # 合并
git branch -d dev  # 删除
git branch -M main  # 将本地主分支改为main（与GitHub远程主分支保持一致）
```

如果两个分支同时修改了同一处，合并时会产生冲突，此时冲突的文件会变为未追踪状态，文件中会用=======上下分割标识出两个分支冲突的部分，需要手动解决后再次add到暂存区后提交。

## 推送

```shell
# 把远程仓库某分支最新代码取回本地，但不会自动合并
# 此时可以git status查看下，然后执行git merge合并
git fetch [origin main]
# 从已跟踪服务器拉取并自动合并
git pull [origin main] # 等同于：git fetch + git merge

# 推送到远程仓库
git push [-u origin main]  # 首次推送加-u参数，设置默认仓库和分支
```

## 标签

```shell
git tag  # 查看标签
git tag -a v1.0 -m "version one"  # 新建标签
git push origin v1.0  # 推送到远程（推送分支时不会带上标签，所以标签需要单独推送）

git tag -d v0.5  # 删除本地标签
git push origin --delete v0.5  # 删除远程标签
```

## 项目托管平台

- GitHub
- GitLab(极狐)
- Bitbucket：Jira母公司Atlassian旗下产品
- Gitee(码云)：开源中国和工信部联合推出
- Coding：并购了Gitcafe，后又被腾讯云全资收购

## 项目协作

常见的有三种形式

- Git Flow
- Github Flow
- Gitlab Flow

```bash
# 先fork

git clone me.git  # 克隆自己的远程库
git remote add upstream common.git  # 关联公用的远程库，命名为upstream

git checkout -b dev  # 创建dev分支

git add .  # 最好每个修改点一次add
git commit -m "xxx"  # 最好每个修改点一次commit
git checkout master  # 切回主分支
git merge dev  # 合并分支
git branch -d dev  # 删除dev分支

git fetch upstream  # 拉取upstream最新的改动
git rebase upstream/master  # 如果拉取到新的改动则合并到本地
git add 有冲突修改的文件
git rebase --continue  # 继续合并冲突
git push origin master  # 推送到自己的远程分支

# 提交合并
```
