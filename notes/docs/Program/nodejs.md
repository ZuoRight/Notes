# Node.JS

```shell
node --version  # 查看版本
node demo.js  # 运行脚本

node  # 进入交互式环境 REPL（Read-Eval-Print Loop），类似于浏览器的控制台
```

## 安装

参考：<https://nodejs.org/en/download/package-manager>

### Windows

直接安装最新安装包即可

### Mac

```shell
brew install node  # 安装
sudo npm install -g npm
```

### Ubuntu

推荐使用 NVM 版本管理器安装

```shell
sudo apt update

# 不推荐，因为Ubuntu 22.04软件源中包含的Node.JS版本是12.22.9
sudo apt install nodejs -y
sudo apt install npm -y

# 卸载
sudo apt purge nodejs
sudo apt autoremove
```

## 版本管理器

### n（不推荐）

使用 n 管理 node 版本前，首先需要一个 node 环境

```shell
npm cache clean --force  # 清除缓存
sudo npm install n -g  # 安装n模块
n stable  # 升级node.js到稳定版本
hash -r  # 查看版本如果显示的还是旧版本，可以新开一个命令行，reset the location hash

# 卸载
sudo n prune
sudo npm uninstall -g n
sudo rm -r /usr/local/n
sudo rm /usr/local/bin/node
```

### NVM（推荐）

> <https://github.com/nvm-sh/nvm>

nvm 不是一个 npm package，而是一个独立软件包

```shell
# 不要使用 sudo 运行，因为这会为 root 用户启用nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
# 安装后重新打开终端
nvm --version

# 安装
nvm list-remote  # 列出所有可安装版本

nvm install node  # 安装最新版本
nvm install --lts  # 安装最新的LTS版本,比如v18.5.0（推荐）
nvm install 18.5.0  # 安装指定版本

nvm ls  # 列出已安装的node
nvm use x.x.x  # 切换当前版本
nvm alias default 12.16.3  # 修改默认版本

# 如果使用 sudo npm/npx 提示没有权限时，需要link下
sudo ln -s "$(which node)" /usr/bin/node
sudo ln -s "$(which npm)" /usr/bin/npm
```

## 包管理

包是一个目录，其中包含一个名为 `package.json` 的文件，描述了包的名称、版本、内容，依赖等

包分两种

- 库：用 `npm` 安装
- 可执行文件：需要用 `npx` 执行（npx在安装node时默认已经安装）

包管理器：NPM、Yarn

### NPM

全球最大开源库生态系统

```shell
npm -v
npm help
npm list -g  # 查看已安装包，不带-g看不到全局的包

# 初始化生成 package.json
npm init -y
# -y: yes
```

不手动初始化也会在安装第一个包时自动生成

```json
{
  "name": "demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

默认安装到 `$PWD/node_modules/...`，代码中通过 `require()` 引入

```shell
npm install [--save-dev] <xxx>
# npm install 可简写为 npm i
"
-g/--global
    本地安装（默认）
    全局安装，可以通过命令行在任何地方调用
--save
    配置信息存储到 package.json 的 dependencies 字段
--save-dev
    配置信息存储到 package.json 的 devDependencies 字段
--force
    强制 npm 获取远程资源，即使磁盘上存在本地副本也是如此
"

# 从 package.json 安装
# --production 只安装dependencies的包，不安装devDependencies的包
# --omit=dev
npm install [--production]

# 从github安装，默认主分支
npm install "https://github.com/ZuoRight/xxx.git#branch-name"
npm install "github:ZuoRight/xxx.git#branch-name"  # 方式2

npm uninstall <name1> <name2>  # 卸载插件

npm install npm -g  # 更新npm自身
npm update <name> [-g] [--save-dev]  # 更新插件，不指定name则更新全部
```

NPM 官方源比较慢，可以使用第三方源，NRM可以用来管理NPM源

```shell
npm install nrm -g
nrm ls  # 列出可用NPM源，带星号的为当前使用源
nrm test [xxx] # 测试源的速度
nrm use cnpm  # 切换源
```

### Yarn

> <https://yarnpkg.com/getting-started/install>

`yarn = npm + npx`

Yarn 是由 Facebook、Google、Exponent 和 Tilde 联合推出了一个新的 JS 包管理工具，弥补 NPM 的一些缺陷而出现的。

NPM 是串行安装，安装慢，如果中间某个包的安装出现了错误，NPM 不会停止，而是会继续安装，这就会导致如果后面还有错误的话将难以排查。

Yarn 是并行安装，安装快，但为了安全考虑有些工具的官方文档提示不建议用yarn安装

```shell
# nodejs 16.10以上版本默认自带yarn，只需要开启corepack即可使用
# Mac可能需要：brew install corepack
corepack enable
yarn --version

# 更新yarn
corepack prepare yarn@stable --activate

yarn help
yarn init [-2]
yarn install  # 安装所有依赖

# 安装指定包
yarn add [package]@[version]
yarn add [package] --dev  # dev dependencies

yarn up [package]  # 更新
yarn remove [package]  # 移除

yarn list  # 查看安装的包，不带global看不到全局的包
```

## 模块

JS 本身是没有模块的概念，模块化的演变经历了一个漫长的过程，从最初的 CommonJS ，到后来的 AMD 和 CMD，再到今天的 ES6 模块化方案

### CommonJS

用于在 Node.js 中导入模块的函数，属于同步操作

```js
// lib.js
module.exports = 'Hello!';  // 导出模块

// app.js
let message = require('./lib');  // 导入模块
console.log(message);  // 输出 'Hello!'
```

### ES6

用于在 ES6 中导入模块的新关键字，属于异步操作

```js
export function foo() {
    pass
};

import {foo} from '模块文件的位置';
```

在 Node 环境下若要使用 import 方式导入，需要 在 `package.json` 中添加配置

```json
{
  "type": "module"
}
```

在 Web 中需要加 module 属性

```js
<script type="module" src="demo.js"></script>
```
