# NPM

node.js包管理器，全球最大开源库生态系统

```bash
npm -v
npm help
npm list -g  # 查看已安装包，不带-g看不到全局的包

npm install  # 安装依赖包
npm install <name> [--save-dev]
"
-g/--global
    本地安装（默认），安装到 $PWD/node_modules/...，通过require()引入
    全局安装，可以通过命令行在任何地方调用
--save
    配置信息存储到 package.json 的 dependencies 字段
--save-dev
    配置信息存储到 package.json 的 devDependencies 字段
--force
    强制 npm 获取远程资源，即使磁盘上存在本地副本也是如此
"

npm update <name> [-g] [--save-dev]  # 更新插件，不指定name则更新全部
npm install npm -g  # 更新npm自身

npm uninstall <name1> <name2>  # 卸载插件
```

## NRM

官方源比较慢，所以通常会用第三方源，可使用NRM管理NPM源

```bash
npm install nrm -g
nrm ls  # 列出可用NPM源，带星号的为当前使用源
nrm test [xxx] # 测试源的速度
nrm use cnpm  # 切换源
```

## N

node.js 管理版本工具

```bash
npm install -g n
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
