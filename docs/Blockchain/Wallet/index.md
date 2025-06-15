# 引言

## 以太坊钱包

- 外部账户

EOA（Externally Owned Accounts），由私钥拥有者控制，可以主动发起交易，codeHash 为空

经过 Keccak-256 哈希算法后，取最后 20 字节再加上 0x，变为 42 个十六进制字符，例如：`0x5e97870f263700f46aa00d967821199b9bc5a120`

> ERC20 地址忽略大小写，TRC20 地址大小写敏感

- 合约账户

CA（Contract Accounts），由智能合约代码控制，只能被动交易，没有私钥

由 0x 开头的 42 个十六进制字符，例如：`0x06012c8cf97bead5deae237070f9587f8e7a266d`

- 抽象账户

Account Abstraction 是指将两种不同的账户合并成一种

## 比特币钱包

比特币地址是交叉兼容的，每种类型的地址都可以给任意类型的地址发送资金

手续费不同：`原生隔离见证地址（bc1开头） < 隔离见证兼容地址（部分3开头） < 传统地址（1开头） < 多签地址（部分3开头）`

![20230509234516](https://image.zuoright.com/20230509234516.png)

### P2PKH

Pay to PubKey Hash 付款至公钥哈希，最初的传统地址（Legacy）

假设 Alice 给 Bob 转账，相当于将资产放入保险箱，用 Bob 的公钥加密，Bob 只有在向他人转账的时候，才会使用私钥签名打开保险箱，然后将其锁入另一个收款方的保险箱

`Base58(0x00 + Hash160(Public Key) + Checksum)`

编码后 1 开头，34个字符，不包含 0、O、l、I 等容易混淆的字符，比如：`1MbeQFmHo9b69kCfFa6yBr7BQX4NzJFQq9`

```text
OP_DUP 

OP_HASH160 

(Bob 收款地址蕴含的 Public Key Hash) 

OP_EQUALVERIFY 

OP_CHECKSIG
```

### P2SH

Pay to Script Hash 付款至脚本哈希

- 多签地址：P2SH
- 隔离见证兼容地址（Nested SegWit）：P2SH-P2WPKH

`Base58(0x05 + Hash160(Script) + Checksum)`

编码后 3 开头，34个字符，比如：`3EmUH8Uh9EXE7axgyAeBsCc2vdUdKkDqWK`

### P2WPKH / Bech32

专为隔离见证设计的格式，2017 年底由 BIP173 定义

原生隔离见证地址（Native SegWit）

Base32 编码，bc1 开头，42个字符，不区分大小写，只包含数字和小写字母：0～9，a~z，比如：`bc1qj89046x7zv6pm4n00qgqp505nvljnfp6xfznyw`

QR 码更小，更容易防错，更安全，更高效，手续费更低

### P2TR / Taproot

## ENS

<https://app.ens.domains/>

类似 DNS 的以太坊域名服务 ENS（Ethereum Name Service）

钱包地址类似于IP，可以绑定域名，方便记忆

比如V神的域名为：[vitalik.eth](https://app.ens.domains/vitalik.eth)

```js
// 将ENS解析为地址
const addressVitalik = await provider.resolveName("vitalik.eth")
```

## 钱包分类

- 硬件钱包：主要是把私钥管理在离线的硬件设备中，在硬件设备中集成钱包签名算法，比如 Ledger、OneKey
- 多链非托管钱包：OKX Wallet、Bitget Wallet（原 BitKeep Wallet）
- MPC（Multi-Party Computation）多方计算钱包，比如 Fireblocks、Safeheron：私钥被分片（数学拆分），无完整私钥，多方协作计算签名
- 多签钱包，比如 Gnosis Safe、Cobo、BitGo：多个私钥分别签名，组合成完整交易
- EOA 外部账户钱包，比如 MetaMask
- AA（Account Abstraction Wallet）账户抽象钱包：将钱包本身变成智能合约，可自定义签名逻辑（如社交恢复、多签）
- 社交恢复钱包：通过守护人机制恢复账户，无需助记词，依赖可信第三方协作

大多数 DApp 通常都支持 WalletConnect 协议扫描二维码连接钱包，V1 建立在 WebSockets 协议上，V2 支持更多传输协议，并增强了安全性和可扩展性。

## 多签

为了避免一个私钥的丢失导致地址的资金丢失，引出了多重签名机制，可以实现分散风险的功能。

假设 N 个人分别持有 N 个私钥，需要要其中 M 个人同意签名才可以动用某个联合地址的资金

最常见的多重签名是 2-3 类型。例如，一个提供在线钱包的服务，为了防止服务商盗取用户的资金，可以使用 2-3 类型的多重签名地址，服务商持有 1 个私钥，用户持有 2 个私钥，一个作为常规使用，一个作为应急使用。这样正常情况下，用户只需使用常规私钥即可配合服务商完成正常交易，服务商因为只持有 1 个私钥，因此无法盗取用户资金。如果服务商倒闭或者被黑客攻击，用户可使用自己掌握的两个私钥转移资金。

## 使用 MetaMask

参考：<https://github.com/WTFAcademy/WTF-Ethers/tree/main/ET01_Metamask>

首先需要安装 MetaMask 浏览器插件，一定要到官网下载：<https://metamask.io/download/>

安装后浏览器会给每个页面注入一个 `window.ethereum` 对象，用于和钱包交互

Ether.js 提供了 `BrowserProvider` 对象封装了一个标准的 Web3 Provider 与其交互

```solidity
const provider = new ethers.BrowserProvider(window.ethereum)

// 读取钱包地址
const accounts = await provider.send("eth_requestAccounts", []);
// 获取ChainID
const { chainId } = await provider.getNetwork()
// 读取钱包ETH余额
const signer = await provider.getSigner()
const balance = await provider.getBalance(signer.getAddress());
```

- 使用 Metamask 签名授权登陆

参考：<https://github.com/WTFAcademy/WTF-Ethers/tree/main/ET02_SignInWithEthereum>
