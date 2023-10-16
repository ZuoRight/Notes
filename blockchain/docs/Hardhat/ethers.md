# Ethers

推荐使用较新的v6版本，与v5相比改动较大：<https://docs.ethers.org/v6/getting-started/>

```shell
npm install ethers --save
```

## 导入

- CommonJS 方式导入

在 Node 环境下导入

```js
const ethers = require('ethers');
```

- ES6 方式导入

在 Node 环境下导入，需要在 `package.json` 中添加配置

```js
import { ethers } from 'ethers';

const main = async () => {
    // code
};

main();

// 运行：node hello.js
```

```json
{
  "type": "module"
}
```

在 Web 中导入需要加 module 属性

```html
<script type="module">
    import { ethers } from "https://cdnjs.cloudflare.com/ajax/libs/ethers/6.7.0/ethers.min.js";
    // Your code here...
</script>
```

## Provider

Provider 是对以太坊网络连接的抽象类，提供了对区块链及其状态的只读访问

- getDefaultProvider

ethers 内置了一些公用的 RPC 方便测试使用，但访问速度有限

```js
const provider = ethers.getDefaultProvider();
```

- jsonRpcProvider

可以通过 Infura 或 Alchemy 等节点服务商获取个人的URL，更快的连接以太坊网络

```js
const INFURA_SEPOLIA_URL = 'xxx'  // 创建个人 API Key
const ALCHEMY_MAINNET_URL = 'https://eth-mainnet.g.alchemy.com/v2/xxx';
const provider = new ethers.JsonRpcProvider(ALCHEMY_MAINNET_URL)
```

### 方法

- `getNetwork()` 查询当前连接网络

```js
const network = await provider.getNetwork();
console.log(network);  // Network {}，不能直接打印
console.log(network.toJSON());  // { name: 'mainnet', chainId: '1' }
```

- `getBlockNumber()` 查询当前区块高度

```js
const blockNumber = await provider.getBlockNumber();
console.log(blockNumber);  // 18363810
```

- `getBlock()` 查询指定区块信息

```js
const block = await provider.getBlock(blockNumber);  // 指定区块高度
console.log(block);

// Block {
//   provider: JsonRpcProvider {},
//   number: 18363810,
//   hash: '0x5b7eeece2fce2816d7bd65d3a5833150adc25f9cf974ab254a9f4cf32c66b69e',
//   timestamp: 1697469815,
//   parentHash: '0xd64a0639df5147b01ed3d9488f10df1456190902a3b60062f4ca69fad5bfcafb',
//   nonce: '0x0000000000000000',
//   difficulty: 0n,
//   gasLimit: 30000000n,
//   gasUsed: 13188166n,
//   miner: '0x95222290DD7278Aa3Ddd389Cc1E1d165CC4BAfe5',
//   extraData: '0x6265617665726275696c642e6f7267',
//   baseFeePerGas: 13127404159n
// }
```

- `getFeeData()` 查询当前建议的Gas设置

```js
const feeData = await provider.getFeeData();
console.log(feeData);  // 返回数据值类型为bigint

// FeeData {
//   gasPrice: 10734724525n,
//   maxFeePerGas: 22439449050n,
//   maxPriorityFeePerGas: 1000000000n
// }
```

- `getBalance()` 获取指定地址余额

```js
const balanceWei = await provider.getBalance(`vitalik.eth`);  // 原生支持ENS
const balanceETH = ethers.formatEther(balanceWei);  // 将单位从默认的wei转换为ETH
console.log(`ETH Balance of vitalik: ${balanceETH} ETH`);
```

- `getTransactionCount()` 查询指定地址历史交易次数

```js
const txCount = await provider.getTransactionCount("vitalik.eth");
console.log(txCount);  // 1142
```

- `getCode()` 查询指定合约的 bytecode

```js
const code = await provider.getCode("0xc778417e063141139fce010982780140aa0cd5ab");
console.log(code);

// 0x6060604052361561010f5763ffffffff7c010000......0000060003504166306fdde03811461011157806307......d59296451a3a0c1683c70029
```

## BigNumber

可以利用`ethers.BigNumber.from()`函数将string，number，BigNumber等类型转换为BigNumber

```js
const decimals = BigNumber.from("1000000000000000000");
const oneGwei = ethers.BigNumber.from("1000000000")

oneGwei.add(1).toString()  // 加
oneGwei.sub(1).toString()  // 减
oneGwei.mul(1).toString()  // 乘
oneGwei.div(1).toString()  // 除
oneGwei.mod(1).toString()  // 取模
oneGwei.pow(1).toString()  // 幂运算
oneGwei.abs(1).toString()  // 绝对值
oneGwei.eq(1000000000)  // 比较，是否相等，返回bool值
```

```ts
import { BigNumber } from "ethers";

const HOUR = BigNumber.from(60 * 60);  // 3600s
const DAY = HOUR.mul(24);  // 86400s
const WEEK = DAY.mul(7);
const MONTH = DAY.mul(30);
const YEAR = DAY.mul(365);
```

## 单位转换

```js
ethers.utils.formatUnits(变量, 单位);
```
