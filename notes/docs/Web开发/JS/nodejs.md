# Node.JS

## 安装 & 升级

参考：<https://nodejs.org/en/download/package-manager>

- Windows

直接安装最新安装包即可

- Mac

```bash
brew install node  # 安装
sudo npm install -g npm # 升级
```

- Ubuntu

```bash
sudo apt install nodejs build-essential -y
sudo apt install npm -y
```

## 使用

```bash
node --version  # 查看版本
node demo.js  # 运行脚本
node  # 进入node环境
```

依赖项

`node_modules/...`

## 包

包是一个目录，其中包含一个名为 `package.json` 的文件，描述了包的名称、版本、内容，依赖等

```bash
# 初始化
npm init -y  # 生成 package.json
"
-y yes
"
```

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

包分两种

- 库
- 可执行文件：需要用 `npx`（安装node时默认已经安装）
