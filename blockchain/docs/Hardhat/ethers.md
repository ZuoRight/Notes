# Ethers

推荐使用较新的 v6 版本：<https://docs.ethers.org/v6/getting-started/>

```shell
npm install ethers --save
```

与 v5 相比改动较大，改动或者迁移可参考：

- <https://docs.ethers.org/v6/migrating/>
- <https://twitter.com/0xAA_Science/status/1645108566221799424>

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

Provider 类是对以太坊网络连接的抽象，提用于连接以及访问（只读）区块链及其状态

- getDefaultProvider

ethers 内置了一些公用的 RPC 方便测试使用，但访问速度有限

```js
const provider = ethers.getDefaultProvider();
```

- jsonRpcProvider

可以通过 Infura 或 Alchemy 等节点服务商获取个人的URL，更快的连接以太坊网络

```js
const INFURA_URL = 'https://sepolia.infura.io/v3/xxx';
const provider = new ethers.JsonRpcProvider(INFURA_URL)
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

## Signer & Wallet

Signer 类是对以太坊账户的抽象，可用于给消息和交易签名并发送到网络更改区块链状态

Signer 类是抽象类，不能直接实例化，需要用它的子类：Wallet

### 创建wallet对象

- 随机私钥

```js
// 私钥由加密安全的熵源生成
const wallet1 = ethers.Wallet.createRandom()
const wallet1WithProvider = wallet1.connect(provider)  // 否则provider为null

console.log(wallet1)
// HDNodeWallet {
//   provider: null,
//   address: '0x7E86Fde7fAEF38c45e4F7f0f40B46987A0F25Da9',
//   publicKey: '0x03d6ab9b45bbb2bbd57b4401f2c3899f4dcf76de1b5472c9d9f19761860da72367',
//   fingerprint: '0xd946a991',
//   parentFingerprint: '0x618c5125',
//   mnemonic: Mnemonic {
//     phrase: 'report edge fatigue embark chef obtain craft always address faith solar cluster',
//     password: '',
//     wordlist: LangEn { locale: 'en' },
//     entropy: '0xb6e8c94f242275310c803c036a433996'
//   },
//   chainCode: '0x720f1b0f727938711cb8b83c44c1e66a6b7f9a50fe9f9942909580f77bb64667',
//   path: "m/44'/60'/0'/0/0",
//   index: 0,
//   depth: 5
// }
```

- 指定私钥

```js
// 指定私钥和provider
const privateKey = '0x227dbb8586117d55284e26620bc76534dfbd2394be34cf4a09cb775d593b6f2b'
const wallet2 = new ethers.Wallet(privateKey, provider)

console.log(wallet2)
// Wallet {
//   provider: JsonRpcProvider {},
//   address: '0xe16C1623c1AA7D919cd2241d8b36d9E79C1Be2A2'
// }

// 这种方式不能获取助记词
const mnemonic = wallet2.mnemonic  // undefined
```

- 从助记词创建

```js
const wallet3 = ethers.Wallet.fromPhrase(mnemonic.phrase)
```

- 从keystore文件创建

```js
const wallet4 = ethers.Wallet.fromEncryptedJson(keystore.json)
```

### 获取钱包信息

```js
const address = await wallet.getAddress()  // 获取钱包地址
const pk = wallet2.privateKey  // 获取私钥
const phrase = wallet.mnemonic.phrase  // wallet2不能获取

// 获取交易次数
const txCount = await provider.getTransactionCount(wallet)  // 参数也可以是address
```

### 发送交易

```js
const tx = {
    to: address1,  // 接收地址
    value: ethers.parseEther("0.001")  // 发送数额
}
// sendTransaction包含发送地址from、请求数据data、nonce等信息
const receipt = await wallet2.sendTransaction(tx)
await receipt.wait()  // 等待链上确认交易

// 打印交易详情
console.log(receipt)
// TransactionResponse {
//   provider: JsonRpcProvider {},
//   blockNumber: null,
//   blockHash: null,
//   index: undefined,
//   hash: '0x20282a0bfcfa53adfe8bc354f14b67952bbec8c50058f600233334efc05a1c46',
//   type: 2,
//   to: '0x6dC688B011ca7d4BdbcdB3Cf42E7F934A44835d6',
//   from: '0xE8187F49c358B3989Be39D34312C6fb421Cc4341',
//   nonce: 82,
//   gasLimit: 21000n,
//   gasPrice: undefined,
//   maxPriorityFeePerGas: 1000000000n,
//   maxFeePerGas: 1000001864n,
//   data: '0x',
//   value: 1000000000000000n,
//   chainId: 11155111n,
//   signature: Signature { r: "0x4d292bf9fedabd389f3844965d5ea41450010bf40ac0669d2e0f7f58566fa3b9", s: "0x0403c675c0c44dd4b7f5ba369e6e16c04fe0827d40cbf3b7b23e83908ad8b1f7", yParity: 1, networkV: null },
//   accessList: []
// }
```

## Contract

Contract 类是对合约（EVM字节码）的抽象，用于与合约交互

- 只读

只能调用合约中的 view 和 pure 函数

```js
const contract = new ethers.Contract('address', 'abi', 'provider');

// 比如读取WETH合约链上信息
const main = async () => {
    const nameWETH = await contractWETH.name()
    const symbolWETH = await contractWETH.symbol()
    const totalSupplyWETH = await contractWETH.totalSupply()
}
main()
```

ABI(Application Binary Interface) 是与以太坊智能合约交互的标准接口，类似于API

可以直接从编译后生成的`artifact`路径下的json文件中获取，如果已开源还可以从EtherScan中获取

```json
[{"constant":true,"inputs":[],"name":"name","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":
...省略...
"type":"address"},{"indexed":false,"name":"value","type":"uint256"}],"name":"Transfer","type":"event"},{"anonymous":false,"inputs":[],"name":"Pause","type":"event"},{"anonymous":false,"inputs":[],"name":"Unpause","type":"event"}]
```

不过这种可读性比较差，ethers 引入了 Human-Readable ABI

```js
const abiERC20 = [
    "function name() view returns (string)",
    "function symbol() view returns (string)",
    "function totalSupply() view returns (uint256)",
    "function balanceOf(address) view returns (uint)",
];
```

- 可读写

可以执行 transaction

```js
const contract = new ethers.Contract('address', 'abi', 'signer');

// 也可以先声明一个只读合约，然后再将只读可约转为可写合约
const contract2 = contract.connect(signer)


const tx = await contract.METHOD_NAME(args [, overrides])  // 发送交易
await tx.wait()  // 等待链上确认交易
// METHOD_NAME 为调用的函数名
// argsw 为参数
// overrides 为可选参数
//     gasPrice
//     gasLimit
//     value：调用时传入的ether（单位是wei）
//     nonce
```

## ContractFactory

```js
// 部署合约
const factoryERC20 = new ethers.ContractFactory(abiERC20, bytecodeERC20, wallet);  // 实例化
const contractERC20 = await factoryERC20.deploy("Test Token", "TTC")  // 部署，并传入constructor参数
console.log(contractERC20.target)  // 合约地址
console.log(contractERC20.deploymentTransaction())  // 合约部署的交易详情
await contractERC20.waitForDeployment()  // 等待合约部署上链

// 与部署成功后的合约交互
console.log(`合约名称: ${await contractERC20.name()}`)
console.log(`合约代号: ${await contractERC20.symbol()}`)
let tx = await contractERC20.mint("10000")
await tx.wait()
```

## 模拟交易

以太坊节点提供了 `eth_call` 方法，让用户可以模拟一笔交易，根据返回结果预知交易能否成功

Ethers 将其封装在了 contract 对象的 `staticCall()` 方法中方便调用

```js
const tx = await contract.函数名.staticCall(函数参数, {override})
// override
//     from: 可以模拟任何msg.sender调用
//     value: msg.value
//     blockTag: 执行时的区块高度
//     gasPrice
//     gasLimit
//     nonce

// 模拟 addressX 给V神转账 1 DAI
const tx = await contractDAI.transfer.staticCall("vitalik.eth", ethers.parseEther("1"), {from: addressX})
// 如果交易可以成功则返回true，否则报错并返回原因
```

## 事件相关

```js
// Solidity 定义 Event
event Transfer(address indexed from, address indexed to, uint256 amount);
// 有索引的（indexed）变量存储在 Topics，反之存在 Data
// 最多可以包含4个索引
```

![20231021110758](https://image.zuoright.com/20231021110758.png)

如图，Address 是合约地址，Topics[0] 是事件哈希：`keccak256("Transfer(address,address,uint256)")`，Topics[1] 和 Topics[2] 分别是 from 和 to 地址，Data 是转账数量。

### 检索事件

- queryFilter

`contract.queryFilter('事件名', 起始区块, 结束区块)`

```js
const block = await provider.getBlockNumber()  // 获取当前区块高度
const transferEvents = await contract.queryFilter('Transfer', block - 10, block)
console.log(transferEvents[0])  // 打印第一个Transfer事件
```

### 监听事件

- 持续监听：`contract.on("事件名", 事件发生时要调用的函数)`
- 监听一次：`contract.once("事件名", 事件发生时要调用的函数)`

```js
// 持续监听USDT合约的Transfer事件，事件发生时打印结果
contractUSDT.on('Transfer', (from, to, value)=>{
    console.log(
        `${from} -> ${to} ${ethers.formatUnits(ethers.getBigInt(value),6)}`
    )
})
```

- 监听过滤

`contract.filters.EVENT_NAME( ...args )`

```js
contract.filters.Transfer(fromAddress, toAddress)  // 过滤所有从a发给b的Transfer事件
contract.filters.Transfer(fromAddress)  // 过滤所有从a发出的～
contract.filters.Transfer(null, toAddress)  // 过滤所有发给b的～
contract.filters.Transfer(null, [to1Address, to2Address])  // 过滤所有发给b或c的～
```

实例

```js
const provider = new ethers.JsonRpcProvider(Infura_URL);
const addressUSDT = '0xdac17f958d2ee523a2206206994597c13d831ec7'  // USDT合约地址
const filterBinanceIn = '0x28C6c06298d514Db089934071355E5743bf21d60'  // 某人Binance的热钱包地址
// 构建ABI
const abi = [
  "event Transfer(address indexed from, address indexed to, uint value)",
  "function balanceOf(address) public view returns(uint)",
];
// 构建合约对象
const contractUSDT = new ethers.Contract(addressUSDT, abi, provider);

// 查询accountA的余额
const balanceUSDT = await contractUSDT.balanceOf(filterBinanceIn)
console.log(`USDT余额: ${ethers.formatUnits(balanceUSDT,6)}\n`)

// 过滤Transfer事件：从accountBinance转出USDT
let filterToBinanceOut = contractUSDT.filters.Transfer(accountBinance);
console.log(filterToBinanceOut);
contractUSDT.on(filterToBinanceOut, (res) => {
    console.log('---------监听USDT转出交易所--------');
    console.log(
        `${res.args[0]} -> ${res.args[1]} ${ethers.formatUnits(res.args[2],6)}`
    )
});

// 过滤Transfer事件：转入accountBinance
let filterBinanceIn = contractUSDT.filters.Transfer(null, accountBinance);
console.log(filterBinanceIn);
contractUSDT.on(filterBinanceIn, (res) => {
    console.log('---------监听USDT转入交易所--------');
    console.log(
    `${res.args[0]} -> ${res.args[1]} ${ethers.formatUnits(res.args[2],6)}`
    )
});
```

## 单位转换

- 转为ether：`ethers.formatEther("变量")`

```js
ethers.formatEther("1")  // '0.000000000000000001'
ethers.formatEther("1000000000000000000")  // '1.0'
```

- 任意转：`ethers.utils.formatUnits("变量", "单位");`

小转大输出类型为`BigInt`，大转小输出类型为`String`

```js
// 默认转换为ether
ethers.formatUnits("1");  // 1000000000000000000n
ethers.formatUnits("1000000000000000000");  // '1.0'

// 可以指定单位
ethers.formatUnits("1", "ether");  // 1000000000000000000n
ethers.formatUnits("1", "gwei")  // 1000000000n
ethers.formatUnits("1", "wei")  // 1n

// 也可以转为任意长度
ethers.parseUnits("1", 3)  // 1000n
```

## BigInt

在 Ethers V5 中需要引入 `BigNumber` 库来实现，四则运算需要使用对应方法

```js
// import { BigNumber } from "ethers";
const { BigNumber } = require('ethers')

const HOUR = BigNumber.from(60 * 60);  // 3600s
const DAY = HOUR.mul(24);  // 86400s
const WEEK = DAY.mul(7);
const MONTH = DAY.mul(30);
const YEAR = DAY.mul(365);

const decimals = BigNumber.from("1000000000000000000");
const oneGwei = ethers.BigNumber.from("1000000000");

oneGwei.add(1).toString()  // 加
oneGwei.sub(1).toString()  // 减
oneGwei.mul(1).toString()  // 乘
oneGwei.div(1).toString()  // 除
oneGwei.mod(1).toString()  // 取模
oneGwei.pow(1).toString()  // 幂运算
oneGwei.abs(1).toString()  // 绝对值
oneGwei.eq(1000000000)  // 比较，是否相等，返回bool值
```

在 Ether V6 中，`BigNumber` 库被移除，替换为 JS ES2020 原生的 BigInt 库

```js
const value1 = 1000n
const value2 = ethers.getBigInt("1000")  // 将其它类型转换为 BigInt
console.log(value1 + 1n)  // 基础运算直接用
console.log(value1 == value2)  // 比较是否相等
```
