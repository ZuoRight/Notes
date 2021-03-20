## Git

```shell
# 查看版本
git --version

# 设置仓库代码提交者信息
git config --global user.name "chonge"
git config --global user.email "chonge@zuoright.com"

# 向远程仓库提交代码时需要验证身份
# 一种是每次输入用户名和密码
# 还可以设置SSH密钥省去验证
ssh-keygen  # 生成密钥
cat ~/.shh/id_rsa.pub  # 查看公钥，将公钥添加到GitHub/Gitlab等仓库设置中
```

``` shell
# 如果是新项目，先在本地创建一个文件夹
mkdir xxx

# 然后初始化，会在项目根目录生成一个.git文件
git init

# 然后再创建个远程仓库，将远程仓库关联与本地仓库关联
git remote add github git@github.com:zuoright/xxx.git  # github为远程库名称，可自定义

# 如果已有远程库，可以直接克隆远程仓库到本地
git clone  git@github.com:zuoright/xxx.git
```

```shell
# 创建 .gitignore 文件，编写忽略规则
# 创建 LICENSE 文件，编写授权声明
# 创建 README.md 文件，编写项目介绍
```

```shell
# 提交代码
git add .
git commit -m "init"

# 推送到远程仓库origin的main分支
# 可以加-u参数设置默认推送的仓库和分支，下次直接git push即可
git branch -M main  # 将本地主分支设置为main
git push [-u] origin main
```

