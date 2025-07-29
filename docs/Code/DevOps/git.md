# Git

- 官网：<https://git-scm.com/>
- 官方文档：<https://git-scm.com/book/zh/v2>

## 安装

- Windows：官网下载 `Git for Windows` 客户端安装即可
- Ubuntu/Debian：`apt install git`
- CentOS/Fedora：`yum install git`
- Mac：可以下载安装包安装，也可以用 `brew install git` 方式安装

Mac 中可以查看 Git 版本触发安装 Xcode CLT，CLT 默认自带 Git，此时用 brew 安装会提示已经存在，根据提示执行 `brew link --overwrite git` 覆盖

```shell
# 查看版本
git --version

# 查看帮助
git help
git xxx -h  # 或 --help
```

## 配置

```shell
# 优先级及影响范围依次为
'
--local 对当前用户、当前仓库有效，默认（.git/config）
--global 对当前用户、所有仓库有效（~/.config/git/config）
--system 对所有用户、所有仓库有效，需要root权限（/etc/gitconfig）
'

# 查看配置
git config --list
git config --list --show-origin  # 显示配置路径
git config --list --global
git config user.name
```

- 配置代码提交者信息

```shell
git config --global user.name "demo"
git config --global user.email "demo@test.com"
```

- 配置密钥

Git 服务器（比如GitLab）或者说远程仓库（比如GitHub）通常会支持两种传输协议：HTTPS 和 SSH

如果使用 SSH 协议传输则需要[配置密钥](https://github.com/settings/keys)进行认证

```shell
# 如果之前没生成过密钥，需要先生成
ssh-keygen
'
回车，会提示保存位置
再回车，如果已存在则会提示是否重新生成
然后会提示设置密码，可以为空直接回车
如果提示输入密钥类型，推荐选择ed25519，没提示一般默认会是rsa类型
'

ls ~/.ssh
'
authorized_keys2  id_dsa       known_hosts
config            id_dsa.pub
'
# ed类型的文件名为 id_ed25519.pub

# 查看公钥，将其配置到项目托管平台中
cat ~/.ssh/id_rsa.pub
```

- 配置代理

```shell
# 如果报错：Failed to connect to github.com port 443 after 1 ms: Couldn't connect to server
# 可以加代理试试
git config --global http.proxy 127.0.0.1:10808
```

## 远程仓库

```shell
git remote -v  # 列出添加过的远程库
git remote show origin_name  # 查看远程库详细信息
git remote rename old_name new_name  # 修改远程库简写名
git remote remove demo  # 删除已添加的远程库，remove可简写为rm

# 如果远程库修改了名称，可以修改url重新关联
git remote set-url origin new_url
```

### 获取仓库

```shell
git clone <url>
git clone <url> path_name  # 克隆到自定义目录，比如当前目录「.」
'
url 克隆如果超时就尝试另一个协议试一试
  ssh协议：git@github.com:user/repo.git
  https协议：https://github.com/user/repo.git
-o origin_name 默认为 origin
-b branch 默认拷贝主分支，也可指定分支
--depth 1
  浅克隆 Shallow Clone，只克隆最新 1 次提交（HEAD），而不包含完整的历史记录。
  可以显著减少下载时间和磁盘占用，比如 CI/CD 流水线
'
```

`git clone` 其实是对以下命令的封装

```shell
# 创建新目录
mkdir demo
# 切换到新目录
cd demo
# 初始化，生成 .git 跟踪文件
git init
# 为指定的 url 添加一个远程库，默认名称 origin
git remote add origin_name url
# 获取远程库数据
git fetch origin_name
# 创建并检出一个同远程主分支相同的本地分支
git checkout -b main
```

### fetch

```shell
# 将远程仓库中有但当前仓库没有的所有数据拉取下来，然后存储在本地仓库中
git fetch origin  # 获取所有远程分支信息

# 自动跟踪远程的 origin/dev 分支并在本地创建同名的 dev 分支
git checkout dev

# 如果无法自动匹配，也可以手动指定
git fetch origin dev  # 获取远程的 dev 分支信息
git checkout -b dev origin/dev
```

### pull

```shell
# 从已跟踪服务器拉取并自动合并，等同于：git fetch + git merge
git pull [origin main]
'
不带参数，先尝试快进合并，如果不行再进行正常合并生成一个新的提交。
--ff-only 快进合并，只尝试快进合并，如果不行则终止当前合并操作。
--no-ff  普通合并，禁止快进合并，即无论是否能快进合并，最后都会进行正常合并生成一个新的提交。
--rebase  变基合并，先尝试快进合并，如果不行再进行变基合并。
'
```

### push

将提交后的代码推到远程仓库

```shell
# 从本地仓库中，取出本地 dev 分支的所有最新提交，推送到名为 origin 的远程仓库中的 dev 分支（默认与本地分支同名）
git push origin_name dev
'
如果远程仓库中已有名为 dev 的分支，则会将变更合并到远程的 dev 分支中
如果没有冲突，远程分支将被更新到与本地分支相同的状态

如果远程仓库中没有名为 dev 的分支，则会创建一个新的 dev 分支，然后将变推送到远程的 dev 分支中

如果本地 dev 分支与远程 dev 分支的历史没有直接的线性关系（比如本地分支已经被重新基于其他提交），可能会遇到一个非快进（non-fast-forward）推送错误。
在这种情况下，可能需要使用 --force 或 --force-with-lease 选项进行强制推送，或者合并变更到你的本地分支再尝试推送
'

# 也可以提交到与远程库不同名的分支
git push origin_name 本地分支:指定远程分支

# 如果第一次推送一个分支到远程，或者希望设置追踪信息，可以使用 -u 或 --set-upstream 选项
# 推送代码的同时还会设置 dev 分支的上游（upstream）追踪信息，之后便可以使用 git pull 或 git push 而无需指定远程库和分支名
git push -u origin dev


# 提交后返回的一些信息
'
To xxx.git
  1edee6b..fbff5bc  main -> main
  (oldref..newref fromref → toref)
'
```

```shell
# 删除远程分支（仅移除指针，一段时间后才会被垃圾回收，这期间可以恢复）
git push origin --delete dev
```

## 本地仓库

### 查看状态

```shell
git status
'
On branch main
当前分支
Your branch is up-to-date with "origin/main".
当前分支是最新的，没有落后远程分支
nothing to commit, working directory clean
工作区是干净的，没有文件需要提交
'

# 文件主要有以下状态
'
未跟踪 new file untracked
已跟踪
  未修改 unmodified
  已修改未暂存 modified unstage
  已暂存准备提交 modified staged
'
```

### 查看修改

```shell
git diff  # 查看未暂存的改动，即工作区和暂存区的比较
git diff --staged  # 查看已暂存但未提交的改动，也可用--cached
git diff branch1 branch2  # 比较两个提交记录的差异

# 撤销修改
git checkout -- xxx
```

### add

```shell
# 将未跟踪或已修改文件添加到暂存区，变为已跟踪已暂存状态
git add xx  # 添加某个
git add .  # 添加全部，也可用-A或--all参数

# 部分暂存
git add --patch
```

### 重命名

```shell
# 如果只是重命名文件而不修改其内容时可以这样做
git mv old_name new_name
```

### commit

```shell
# 创建快照，将当前分支指针向前移动
git commit -m "init"  # 不加-m会打开默认编辑器编辑
'
-a，可以不用先git add，直接提交
--ament 重新提交，可以覆盖上一次的提交
'

# 如果切换分支但开发到一半不想提交，可以先临时储存
git stash
```

### 查看历史

<https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History>

```shell
git log  # 时间倒序列出所有提交记录
'
--patch/-p 显示每次提交所引入的差异
--graph 在日志旁以 ASCII 图形显示分支与合并历史
--pretty=oneline 每个提交记录放在一行显示
--pretty=format:"%h - %an, %ar : %s" 显示格式
'
```

## 分支

```shell
# 查看分支
git branch  # 默认只显示本地分支，-r 只显示远程分支，-a 显示所有本地和远程分支
'
-v 显示最后一次提交
--merged 已合并到当前分支的分支
--no-merged main 没有合并到当前分支的分支

-vv 查看各分支正在跟踪的上游分支
-u 或 --set-upstream-to 显示设置当前分支正在跟踪的上游分支，clone 或 checkout 时会自动设置

如果分支有很多，返回会分页（显示冒号:）
  向下翻页：⬇，或者「空格键」直接显示剩余所有，最后会显示 END
  向上翻页：⬆，或者「b」键回到开始
'

# 新建分支
git branch dev

# 删除分支，删除未合并的分支需要用-D强制删除
git branch -d dev1 dev2

git branch -m old_name new_name  # 修改本地分支名称
git branch -M main  # 修改本地主分支（改为main，与GitHub远程主分支保持一致）
```

- 切换分支 checkout

```shell
# 切换分支，就是改变指针指向，即检出
git checkout dev  # 也可以用：git switch dev

# 新建并切换分支
git checkout -b dev

# 新建自远程xxx分支，并切换
git checkout -b test origin/xxx
git checkout --track origin/xxx  # 简写
git checkout xxx  # 如果本地没有但恰巧远程有唯一匹配的xxx分支，则等同于上面命令
```

## 标签

```shell
git tag  # 列出标签
git tag -l "v1.8.5*"  # 模糊匹配

git show tag v1.0  # 查看标签信息

git tag tmp1  # 打轻量标签
git tag -a v1.0 -m "version 1.0"  # 打附注标签
git tag -a v1.2 9fceb02  # 给过去的某次提交打标签

git push origin v1.0  # 推送分支时不会带上标签，所以标签需要单独推送
git push origin --tags  # 推送所有标签

git tag -d v0.5  # 删除本地标签
git push origin --delete v0.5  # 删除远程标签
```

## 合并

![20241130154226](https://image.zuoright.com/20241130154226.png)

### merge

将其它分支合并到当前分支，创建一个新的合并提交，如果没有冲突则可以快进合并

如果两个分支同时修改了同一处，合并时会产生冲突，此时冲突的文件会变为未追踪状态，文件中会用 ``=======` 上下分割标识出两个分支冲突的部分，需要手动解决后再次 add 到暂存区后提交。

```shell
# 将 dev 合并到 main
git checkout main
git merge dev
'
如果有冲突，可以选择终止合并
git merge --abort

也可以解决冲突后继续合并
git add 有冲突修改的文件
git commit -m "冲突修改后的说明"
'
```

### rebase

变基合并，改变分支的分叉合并点

比如，基于本地 main 分支分叉出一个 dev 分支，然后远程 main 分支有新的更新（此时本地分支被称之为偏离分支），使用 rebase 合并更新，dev 分支的分叉点将变为基于最新的 main 分支

```shell
git checkout dev
# 基于本地的远程 main 分支 rebase（记得先 fetch），当然也可以基于本地 main 分支（记得先 pull）
git fetch origin main
git rebase origin/main
# 等价于
git pull --rebase origin main

'
如果有冲突，可以取消合并
git rebase --abort

也可以解决冲突后继续合并
git add 有冲突修改的文件

解决冲突后继续合并
git rebase --continue
'
# 在 rebase 过程中，冲突可能在每个被重新应用的提交上发生，需要逐个解决，反复重复以上过程
```

Git 会找到 `dev` 和 `origin/main` 分支的共同祖先，取回所有分叉后的更新重放到 dev 分支，然后再拼接上 dev 分支的提交（已提交的哈希值将会被改变），使 dev 看起来就像基于最新的 main 分支检出然后提交的一样

通常建议只对尚未推送的本地修改执行变基操作，如果本地分支已经推送过远程，并且有人基于它开发，那么 rebase 后会导致其他人的本地仓库与远程仓库不一致

### cherry-pick

只将某个分支上的指定提交合并到当前分支

比如在 dev 分支修改一个 bug，然后只想将这次修复提交先合并到 main 分支，而不合入其它提交

```shell
git checkout main
git cherry-pick commit_id
```

## 回滚

```shell
# 回滚到某个版本，注意，之后的版本都将消失
git reset [--参数] 版本 [指定文件]
git push --force  # 回滚后要推向远程，否则会报错

# 参数
'
--soft 回到 commit 前的状态
--mixed 回到 add 前的状态，默认
--hard 回到工作区修改前状态，重置
'

# 版本，有3种形式
'
版本号：后七位即可
标签
HEAD指针
  HEAD 当前版本
  HEAD^ 上个版本，如果有多个父提交，则表示第1个父提交HEAD^1
  HEAD^^ 上上版本

  HEAD~ 上个版本，如果有多个父提交，则表示直接的父提交，表示其它父提交需要用HEAD^n
  HEAD~100 上100个版本
'
```

比如不小心提交了隐私记录，可以reset到这次提交前的版本

> 注意，删除后通过 <https://github.com/ZuoRight/demo/commit/hash_id> 依然可以访问到

```shell
git log  # 查看提交记录，找到包含隐私的那一次提交记录，copy它上一个版本的hash_id
git reset --hard hash_id
git push --force origin HEAD
```

### 取消暂存

```shell
git reset HEAD

# 变为未跟踪状态但不删除文件
git rm --cached xxx

# 删除已跟踪文件，如果已修改或已暂存，需要加 -f
git rm xxx
```

### 取消合并

- 如果合并尚未提交

```shell
git merge --abort
# 或
git reset --merge
```

- 如果合并已提交，但还没推送到远程

```shell
git reset --soft HEAD^  # 撤回到合并前状态，保留更改
git reset --hard HEAD^  # 完全撤回，丢弃更改
```

- 如果合并已提交，并且已推送到远程

```shell
git revert -m 1 HEAD  # -m 1 表示保留第1个父提交的更改
```

## 完善仓库

```shell
touch README.md  # 项目介绍文件
touch LICENSE  # 授权声明文件

# 忽略规则文件
# 一个项目可以有多个，通常放在根目录一个，子目录也可以有额外的
touch .gitignore
# 各语言通用模版：https://github.com/github/gitignore
# 规则举例
'
#  表示注释
!  取反
?  匹配任意1个
*  匹配任意个字符
**  匹配任意中间目录
[abc]  匹配其中任意一个
[0-9]  匹配0到9

*.a  忽略所有.a文件
*.[oa]  忽略以.o或.a结尾的文件
*~  忽略所有以～结尾的文件

!lib.a  跟踪所有的 lib.a，即便你在前面忽略了 .a 文件

/demo  只忽略当前目录下的 demo 文件，而不忽略 xxx/demo
demo/  忽略任何目录下名为 demo 的文件夹

doc/*.txt  忽略 doc/notes.txt，但不忽略 doc/server/arch.txt
doc/**/*.pdf  忽略 doc/ 目录及其所有子目录下的 .pdf 文件
'
```

## Git Flow

```shell
# 克隆远程仓库到本地master分支
git clone git@code.xxx.net:demo/test.git
'
参与没有代码推送权限的项目，比如开源项目，可以
1. 先 fork 副本到自己的远程仓库
2. 然后 clone 副本到本地，创建新分支进行修改提交
3. push 到自己的远程仓库，然后创建一个 PR(Pull Request) 给开源项目，在 GitLab 中叫做 MR（Merge Requests）

关联公用的远程库，假设命名为 upstream
git remote add upstream common.git
'

# 新建并切换到新分支开发
git checkout -b dev
git add .  # 最好每个修改暂存一次，而不是都混在一起
git commit -m "改动说明"


git fetch origin master  # 获取远程分支是否有新的改动
git rebase origin/master  # 如果获取到新的改动则可以变基合并
# 等价于
'
git pull --rebase origin master
'

# 也可以使用 merge 合并
'
git checkout master  # 切回本地主分支
git pull origin master  # 拉取最新代码，也可以 fetch + merge
git merge master  # 将 master 合并到 dev
'


# 将 dev 分支推送到远程
git push origin dev
# 删除已被合并的分支
git branch -d dev
```

## 其它

- `... origin xxx` 操作远程仓库时使用空格的形式
- `... origin/xxx` 操作本地远程分支时使用斜杠的形式
