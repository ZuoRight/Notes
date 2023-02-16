# 引言

```bash
npm init -y  # 生成 package.json
npm install --save-dev hardhat  # 安装hardhat
npx hardhat  # 生成 hardhat.config.js
<<"COMMENT"
888    888                      888 888               888
888    888                      888 888               888
888    888                      888 888               888
8888888888  8888b.  888d888 .d88888 88888b.   8888b.  888888
888    888     "88b 888P"  d88" 888 888 "88b     "88b 888
888    888 .d888888 888    888  888 888  888 .d888888 888
888    888 888  888 888    Y88b 888 888  888 888  888 Y88b.
888    888 "Y888888 888     "Y88888 888  888 "Y888888  "Y888

👷 Welcome to Hardhat v2.12.7 👷‍

✔ What do you want to do? · Create an empty hardhat.config.js
✨ Config file created ✨

Give Hardhat a star on Github if you're enjoying it! 💞✨

     https://github.com/NomicFoundation/hardhat
COMMENT
```

```js
/** @type import('hardhat/config').HardhatUserConfig */
module.exports = {
  solidity: "0.8.17",
};
```

```bash
npx hardhat compile  # 编译成字节码和元数据存储到artifacts/路径

npx hardhat node  # 启动 hardhat network
npx hardhat run --network localhost scripts/deploy.js  # 执行部署脚本

npx hardhat console --network localhost  # 使用当前网络连接控制台，与合约交互
```

- unit test

```bash
npm install --save-dev chai

npx hardhat test --grep "aa bb" --network localhost
```
