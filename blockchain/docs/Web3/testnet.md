# 测试网

## 分类

### live network

公共网络提供的持久化区块链，即指 `Ethereum` 的 `Mainnet`(主网)、`Goerli`(测试网)等，通常使用以太坊节点与公网交互

可以借助`Geth`等客户端运行自己的个人节点，也可以使用 RPC Node Provider 提供的托管节点，比如：

- `Infura`
- `Alchemy`
- `QuickNode`

### development network

本地开发临时搭建的个人区块链，可以借助一些工具来搭建，比如：

- `Ganache` (Truffle 的组件)
- `Hardhat Network` (Hardhat 的组件)
- `Anvil` (Foundry 的组件)
- `Geth Dev`

## Chain ID

Chain ID 是在 EIP-155 中引入的，以防止主 ETH 和 ETC 链之间的重放攻击

> 在 EIP-155 之后，ETH 的 chainID 为 1 ，而 ETC 的 chainID 为 61，但它们具有相同的网络 ID 1

通过在签名信息中加入Chain ID， 避免一个交易在签名之后被重复在不同的链上提交。

可以通过 Chainlist 网站查询 Chain ID：<https://chainlist.org/?search=eth&testnets=true> (old url: chainid.network)

Chainlist 是 EVM 网络的列表。用户可以使用这些信息将他们的钱包和 Web3 中间件提供商连接到适当的Chain ID 和网络 ID，以连接到正确的链。

```bash
# 常见网络ID
Mainnet: 1
Goerli: 5
Sepolia 11155111
Ganache: 1337
Hardhat-Network: 31337
```

## 水龙头

### Goerli

以太坊2.0后有些测试网已经废弃，Goerli将会是最主要的POS测试网，像Uniswap、OpenSea等大项目都在Goerli测试网搭建了测试环境，但是Goerli测试网代币比较难搞，水龙头比较稀缺

- <https://goerli-faucet.pk910.de>  1/12h PoW挖矿获取
- <https://chaineye.tools/faucet>  0.1/24h 需要Twitter验证
- <https://goerlifaucet.com>  0.02/24h 需要主网有0.0001ETH
- <https://faucet.quicknode.com/drip>  0.1/12h 需要主网有0.0001ETH，到账时间太慢
- <https://testnetbridge.com/>  付费购买，0.11U/ETH

### Sepolia

- <https://sepoliafaucet.com>  0.5/24h 推荐
- <https://access.rockx.com/faucet-sepolia>  0.5/24h 比较麻烦
- <https://www.infura.io/faucet/sepolia>  0.5/24h，需要主网有0.001ETH
- <https://sepolia-faucet.pk910.de>  2.5/12h，PoW 挖矿，耗费GPU

## Ganache

- UI文档：<https://trufflesuite.com/docs/ganache/>
- RPC交互式文档：<https://ganache.dev/>
- CLI文档：<https://github.com/trufflesuite/ganache#readme>

Ganache 是 Truffle Suite 之一

- 安装

如果只使用UI版本可直接下载安装包，命令行版可使用NPM来安装

```shell
npm install ganache -g  # ganache-cli已被弃用，最新为ganache
ganache --version  # ganache v7.7.2 (@ganache/cli: 0.8.1, @ganache/core: 0.8.1)
ganache --help
```

- 运行

有Tx才会产生新的区块

```shell
# 如果运行了UI需要关闭，否则可能会冲突
# 如果每次运行想保持生成的测试账户地址和私钥不变，可以加参数：-d（daterministic 确定性的）
ganache [-d]

# 每一次调用是一个json rpc调用区块链来进行交互
# Ganache控制台日志
<<'COMMENT'
eth_getTransactionCount
eth_gasPrice
eth_chainId
eth_estimateGas
eth_sendRawTransaction

  Transaction: 0xb9a6121bfde71828cd8c7256406a0d8c803ac7f8c71951588b3fb60607028946
  Contract created: 0xe78a0f7e598cc8b0bb87894b0f60dd2a88d6a8ab
  Gas usage: 471224
  Block number: 1
  Block time: Sat Dec 31 2022 01:45:57 GMT+0800 (中国标准时间)

eth_getTransactionReceipt
eth_chainId
eth_call
COMMENT
```
