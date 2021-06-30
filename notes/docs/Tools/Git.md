# Git

- 官网：<https://git-scm.com/>
- 官方文档：<https://git-scm.com/book/zh/v2>

## 安装

- Windows：官网下载`Git for Windows`客户端安装即可
- Mac：推荐`brew install git`方式安装，如果安装了Xcode会默认自带
- Ubuntu：`sudo apt-get install git`
- CentOS：`sudo yum install git`

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
ssh-keygen
# 查看公钥，将公钥添加到GitHub/Gitlab等仓库设置中
cat ~/.shh/id_rsa.pub
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
# 查看分支
git branch

# 新建分支
git branch dev

# 切换分支
git checkout dev  # new: git switch dev
git checkout -b dev  # 创建并切换分支

# 合并分支
git merge dev

# 删除分支
git branch -d dev
```

如果两个分支同时修改了同一处，合并时会产生冲突，此时冲突的文件会变为未追踪状态，文件中会用=======上下分割标识出两个分支冲突的部分，需要手动解决后再次add到暂存区后提交。

## 推送

```python
# 将本地主分支设置为main(与GitHub默认主分支main保持一致)
git branch -M main

# 把远程仓库某分支最新代码取回本地，不会自动合并
git fetch origin/master
# 合并分支
git merge
# 从已跟踪服务器拉取并自动合并
git pull  # 等同于：git fetch + git merge

# 推送到远程仓库
git push [-u origin main]  # # 首次推送加-u参数，设置默认仓库和分支
```
