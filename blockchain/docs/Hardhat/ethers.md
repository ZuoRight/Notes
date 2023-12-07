# Ethers

推荐使用较新的 v6 版本：<https://docs.ethers.org/v6/getting-started/>

```shell
npm install ethers --save
```

与 v5 相比改动较大，改动或者迁移可参考：

- <https://docs.ethers.org/v6/migrating/>
- <https://twitter.com/0xAA_Science/status/1645108566221799424>

## 导入

- Node 环境下导入

```js
// const ethers = require('ethers');  // CommonJS 方式
import { ethers } from 'ethers';  // ES6 方式

const main = async () => {
    // code
};

main();

// 运行：node hello.js
```

注意：ES6 方式导入需要在 `package.json` 中添加配置

```json
{
  "type": "module"
}
```

- Web 中导入，需要加 module 属性

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
const provider = ethers.getDefaultProvider()
```

- jsonRpcProvider

可以通过 Infura 或 Alchemy 等节点服务商获取个人的URL，更快的连接以太坊网络

```js
const INFURA_URL = 'https://sepolia.infura.io/v3/xxx'
const provider = new ethers.JsonRpcProvider(INFURA_URL)
```

### 方法

- `getNetwork()` 查询当前连接网络

```js
const network = await provider.getNetwork();
// console.log(network);  // Network {}，不能直接打印
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
const wallet = ethers.Wallet.createRandom()
const walletWithProvider = wallet.connect(provider)  // 否则provider为null

console.log(wallet)
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
const privateKey = '0x...'
const wallet = new ethers.Wallet(privateKey, provider)

console.log(wallet)
// Wallet {
//   provider: JsonRpcProvider {},
//   address: '0xe16C1623c1AA7D919cd2241d8b36d9E79C1Be2A2'
// }

// 这种方式不能获取助记词
const mnemonic = wallet.mnemonic  // undefined
```

- 从助记词创建

```js
const wallet = ethers.Wallet.fromPhrase(mnemonic.phrase)
```

- 从keystore文件创建

```js
const wallet = ethers.Wallet.fromEncryptedJson(keystore.json)
```

### 获取钱包信息

```js
const address = await wallet.getAddress()  // 获取钱包地址
const pk = wallet.privateKey  // 获取私钥
const phrase = wallet.mnemonic.phrase  // 指定私钥方式生成的钱包不能获取助记词

// 获取交易次数
const txCount = await provider.getTransactionCount(wallet)  // 参数也可以是address
```

## Contract

Contract 类是对合约（EVM字节码）的抽象，用于与合约交互

ABI(Application Binary Interface) 是与以太坊智能合约交互的标准接口，类似于API

可以直接从编译后生成的 `artifact` 路径下的 json 文件中获取，如果已开源还可以从 EtherScan 中获取

```json
[
    {...},
    {
        "inputs":[

        ],
        "name":"name",
        "outputs":[
            {
                "internalType":"string",
                "name":"",
                "type":"string"
            }
        ],
        "stateMutability":"view",
        "type":"function"
    },
    {
        "inputs":[
            {
                "internalType":"address",
                "name":"account",
                "type":"address"
            }
        ],
        "name":"balanceOf",
        "outputs":[
            {
                "internalType":"uint256",
                "name":"",
                "type":"uint256"
            }
        ],
        "stateMutability":"view",
        "type":"function"
    },
    {
        "inputs":[
            {
                "internalType":"address",
                "name":"to",
                "type":"address"
            },
            {
                "internalType":"uint256",
                "name":"amount",
                "type":"uint256"
            }
        ],
        "name":"transfer",
        "outputs":[
            {
                "internalType":"bool",
                "name":"",
                "type":"bool"
            }
        ],
        "stateMutability":"nonpayable",
        "type":"function"
    },
    {...}
]
```

不过这种可读性比较差，ethers 引入了 Human-Readable ABI

输入程序需要用到的函数，逗号分隔，ethers会自动帮你转换成相应的abi

```js
const abiERC20 = [
    ...,
    "function name() view returns (string)",
    "function balanceOf(address) view returns (uint)",
    "function transfer(address, uint256) public returns (bool)"
    ...
];
```

### 只读

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

### 可读写

可以执行 transaction

```js
const contract = new ethers.Contract('address', 'abi', 'signer');

// 也可以先声明一个只读合约，然后再将只读可约转为可写合约
const contract = contract.connect(signer)

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

## 发送交易

### 转ETH

```js
const ethers = require('ethers');

const main = async () => {
    const INFURA_URL = 'https://sepolia.infura.io/v3/xxx'
    const provider = new ethers.JsonRpcProvider(INFURA_URL)

    const privateKey = '0x...'  // from 地址私钥
    const wallet = new ethers.Wallet(privateKey, provider)
    const tx = {
        to: "0x4e6aC3732f9a02eE4D3A8E68e9540ad48E136ca9",  // to 接收地址
        value: ethers.parseEther("0.0001")  // 发送数量，注意，是字符串格式的
    }
    
    try {
        const transferTx = await wallet.sendTransaction(tx)  // sendTransaction 包含发送地址from、请求数据data、nonce等信息
        await transferTx.wait()  // 等待链上确认交易
        console.log(transferTx)  // 打印交易详情
    } catch (error) {
        console.error("交易失败:", error)
    }
};

main();

`
TransactionResponse {
  provider: JsonRpcProvider {},
  blockNumber: null,
  blockHash: null,
  index: undefined,
  hash: '0xd331f6ad7136cd6dd84696395458ae3aefc014b186b32027469b409f785e1daf',
  type: 2,
  to: '0x4e6aC3732f9a02eE4D3A8E68e9540ad48E136ca9',
  from: '0x62BABAf230c29e611756e10D4520d0490B189aC1',
  nonce: 77,
  gasLimit: 21000n,
  gasPrice: undefined,
  maxPriorityFeePerGas: 11n,
  maxFeePerGas: 373575763n,
  data: '0x',
  value: 100000000000000n,
  chainId: 11155111n,
  signature: Signature { r: "0x350e8e0de7c8ef831a1349773681a1e1206195425c971ad08e5d499ff9663893", s: "0x6d13778806d686851aa7ff6947e581b59ddf511b316bc28d14f5715f91053237", yParity: 1, networkV: null },
  accessList: []
}
`
```

### 转其它代币

```js
const ethers = require('ethers');
require('dotenv').config();

const main = async () => {
    const INFURA_URL = process.env.INFURA_URL;
    const provider = new ethers.JsonRpcProvider(INFURA_URL)

    const privateKey = process.env.PRIVATE_KEY;
    const wallet = new ethers.Wallet(privateKey, provider)

    const usdcContractAddress = '0xD218270a11a3a8E614Ebf8AE8FD3D269a52ac114';
    const usdcAbi = [
        "function transfer(address, uint256) public returns (bool)"
    ];

    async function transferToken(receiverAddress) {
        const usdcContract = new ethers.Contract(usdcContractAddress, usdcAbi, wallet);
        const decimals = 6
        const amount = ethers.parseUnits('1', decimals);  // 发送数量，注意，是字符串格式的
    
        try {
            const transferTx = await usdcContract.transfer(receiverAddress, amount);
            await transferTx.wait();
            console.log(transferTx);
        } catch (error) {
            console.error("代币转账失败:", error);
        }
    }
    
    const receiverAddress = '0x4e6aC3732f9a02eE4D3A8E68e9540ad48E136ca9';
    transferToken(receiverAddress);
};

main();

`
ContractTransactionResponse {
  provider: JsonRpcProvider {},
  blockNumber: null,
  blockHash: null,
  index: undefined,
  hash: '0xac0c865f92cc94e44129d175d43eff3d009cfd0063b2c981e26b04e482702de0',
  type: 2,
  to: '0xD218270a11a3a8E614Ebf8AE8FD3D269a52ac114',
  from: '0x62BABAf230c29e611756e10D4520d0490B189aC1',
  nonce: 78,
  gasLimit: 51641n,
  gasPrice: undefined,
  maxPriorityFeePerGas: 11n,
  maxFeePerGas: 133888205n,
  data: '0xa9059cbb0000000000000000000000004e6ac3732f9a02ee4d3a8e68e9540ad48e136ca900000000000000000000000000000000000000000000000000000000000f4240',
  value: 0n,
  chainId: 11155111n,
  signature: Signature { r: "0x97af365b540e922793179b7818b250ed4a899a4ff81bd369a720a10698e0b0cd", s: "0x3332dc750a1a255b60bea02e2c79af9d6b752a6dc618c0c14fa7fb783a1cab8c", yParity: 0, networkV: null },
  accessList: []
}
`
```

### 模拟交易

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
