# 引言

```bash
npm init -y  # 生成 package.json

# 安装hardhat等
npm install --production  # 从 package.json 安装
npm install --save-dev hardhat
npm install @openzeppelin/contracts
npm install --save-dev @nomiclabs/hardhat-ethers ethers

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

`deploy.js`

```js
async function main() {
  pass;
}

// 推荐这种模式以便能够在任何地方使用 async/await 并正确处理错误
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  })
```

```bash
# 编译成字节码和元数据存储到artifacts/路径
npx hardhat compile

# 启动 hardhat network
npx hardhat node [--port 8545]

# 执行部署脚本
npx hardhat run --network localhost scripts/deploy.js

# 使用当前网络连接控制台，与合约交互
npx hardhat console --network localhost
```

## unit test

```bash
npm install --save-dev chai

npx hardhat test [--grep "xxx"] [--network localhost]
```

```js
// 如果您需要通过从默认帐户以外的帐户（或 ethers.js 术语中的 Signer）发送交易来测试您的代码，您可以在 ethers.js 合约对象上使用 connect() 方法将其连接到不同的帐户
hardhatToken.connect(addr1).transfer()
```

## 参考

- <learn: <https://docs.openzeppelin.com/learn/>>
