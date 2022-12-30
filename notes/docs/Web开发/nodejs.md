# Node.JS

```bash
node -v  # 查看版本
node demo.js  # 运行脚本
node  # 进入node环境
```

## 安装 & 升级

参考：<https://nodejs.org/en/download/package-manager>

- Windows

直接安装最新安装包即可

- Mac

```bash
brew install node  # 安装
sudo npm install -g npm # 升级
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
npm list -g  # 查看已安装包，不带-g看不到全局的包

npm install  # 安装依赖包
npm install <name> [-g] [--save-dev]
"""
-g/--global
全局安装，可以通过命令行在任何地方调用它
本地安装将安装在定位目录的node_modules文件夹下，通过require()调用

--save
将保存配置信息至package.json

-dev
保存至package.json的devDependencies节点，不指定-dev将保存至dependencies节点
像这些express/ejs/body-parser等一般保存在dependencies
"""

npm update <name> [-g] [--save-dev]  # 更新插件，不指定name则更新全部
npm install npm -g  # 更新npm自身

npm uninstall <name1> <name2>  # 卸载插件
```

- NRM

官方源比较慢，所以通常会用第三方源，可使用NRM管理NPM源

```bash
npm install nrm -g
nrm ls  # 列出可用NPM源，带星号的为当前使用源
nrm test [xxx] # 测试源的速度
nrm use cnpm  # 切换源
```

## Yarn

Yarn是由Facebook、Google、Exponent 和 Tilde 联合推出了一个新的 JS 包管理工具，弥补 npm 的一些缺陷而出现的。

npm是串行安装，安装慢，如果中间某个包的安装出现了错误，npm不会停止，而是会继续安装，这就会导致如果后面还有错误的话将难以排查。

yarn是并行安装，安装快，但为了安全考虑有些工具的官方文档提示不建议用yarn安装

```bash
yarn

yarn global add xxx
yarn global add xxx –dev

yarn global remove xxx

yarn global upgrade

yarn global list  # 查看安装的包，不带global看不到全局的包
```
