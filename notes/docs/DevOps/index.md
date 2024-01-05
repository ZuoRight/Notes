# 引言

## VCS

Version Control System 版本控制系统

用于跟踪文本文件的改动，对于二进制文件只能跟踪其大小的变化

- 文本文件：各种源代码、纯文本等
- 二进制文件(Blob, binary large object)：图片、视频、office文件（其实是个压缩包，由很多.xml和其他文件构成，所以属于二进制）

VCS 主要分为

- 本地版本控制系统，比如：RCS
- 集中式版本控制系统，比如：SVN
- 分布式版本控制系统，比如：Git

## 托管平台

- GitHub：现已被微软收购，最大的托管平台
- GitLab：乌克兰企业，在中国创建了合资品牌（极狐），主要以开源版本著称，组织可以私有化部署，当然也提供云托管服务
- Bitbucket：Jira母公司Atlassian旗下产品
- Gitee(码云)：开源中国和工信部联合推出
- Coding：并购了Gitcafe，后又被腾讯云全资收购

## 协作模式

如果没有想参与一个开源项目，但没有代码推送权限，可以

1. 先 fork 副本到自己的远程仓库
2. 然后 clone 到本地，创建新分支进行修改提交
3. push 到自己的远程仓库，然后创建一个 PR(Pull Request) 给开源项目

### Git Flow

```shell
# 克隆仓库master分支到本地
git clone git@code.xxx.net:demo/test.git
# 新建并切换到新分支开发
git checkout -b dev

git add .
git commit -m "改动说明"

# 切回本地master
git checkout master
# 拉取远程master分支最新代码
git fetch origin master
# 如果远程有最新提交则需要rebase
git rebase origin/master

# 切回自己的分支
git checkout dev
# 合并本地master分支
git merge master
# 如果有冲突
"""
可以选择终止合并：git merge --abort
也可以解决冲突后继续合并
"""
git add 解决了冲突的文件
git commit -m "冲突修改后说明"


# 合并并且解决完冲突后push自己的分支到远程
git push origin dev
# 然后在创建MR（Merge Requests）请求合并到origin的master分支
```

### Gitlab Flow

```shell
git clone me.git  # 从自己的远程仓库 clone 到本地
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

### Github Flow

待更新

## CICD

- CI(Continuous Integrationv) 持续集成
- CD(Continuous Deploymen) 持续部署/交付

![20210808174712](http://image.zuoright.com/20210808174712.png)
