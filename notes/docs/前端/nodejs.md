# Node.JS

```bash
node -v  # 查看版本
node demo.js  # 运行脚本
node  # 进入node环境
```

## 升级

- Windows

直接安装最新安装包即可

- Mac

```bash
sudo npm install npm -g
```

- Linux

npm中有一个模块叫做`n`，专门用来管理node.js版本的

```bash
sudo npm cache clean -f
sudo npm install -g n
sudo n stable  # stable稳定版、latest最新版
```

## NPM

node.js包管理器，全球最大开源库生态系统

```bash
npm -v
npm help
npm list  # 查看已安装包

npm install <name> [-g] [--save-dev]
"""
-g
全局安装，可以通过命令行在任何地方调用它
本地安装将安装在定位目录的node_modules文件夹下，通过require()调用

--save
将保存配置信息至package.json

-dev
保存至package.json的devDependencies节点，不指定-dev将保存至dependencies节点
像这些express/ejs/body-parser等一般保存在dependencies
"""

# 更新npm自身
npm install npm -g

# 更新插件，不指定name则更新全部
npm update <name> [-g] [--save-dev]

# 卸载插件
npm uninstall <name1> <name2>
```

## NRM

官方源比较慢，所以通常会用第三方源，可使用NRM管理NPM源

```bash
npm install nrm -g
nrm ls  # 列出可用NPM源，带星号的为当前使用源
nrm test [xxx] # 测试源的速度
nrm use cnpm  # 切换源
```
