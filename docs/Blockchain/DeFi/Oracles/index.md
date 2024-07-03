# 预言机

区块链在设计上本身是一个确定性的封闭系统，可以借助预言机（Oracle）来实现获取可变数据、随机值或者调用API等

但如果引入中心化预言机，将重新引入单点失败风险，所以我们需要去中心化的预言机，比如

- Chainlink
- API3
- Augur

在本地调试时无法使用部署在 Goerli 等公网的 Chainlink 服务，此时可以直接 Copy [chainlink-mix](https://github.com/smartcontractkit/chainlink-mix/tree/main/contracts/test) 提供的代码，也可以使用：`brownie bake chainlink-mix`，或者直接传入 `abi` 和 `address`

## Chainlink

> <https://docs.chain.link/>

- Data Feeds

Chainlink 节点通过去中心化网络从不同的 Exchanges 和数据提供商获取数据，然后使用中位数来计算资产价格，然后在单个交易中将其传递给参考合约、喂价合约或链上数据合约

提供数据的节点可以获得$LINK代币作为激励，如果数据不更新则很快会被踢出网络

- VRF

一种公平且可验证的随机数生成器（RNG），它使智能合约能够在不影响安全性或可用性的情况下获取随机值

- Automate Contracts

监听特定触发器合约（事件或时间，比如每周，或当某种资产价格到达某个价位，或流动性池处于某个状态），一旦满足条件，则以去中心化方式通过 event 触发对应的函数执行

- Connect to ANY API

通过 HTTP GET 和 POST 方法可以定制 Chainlink 节点访问任意外部数据源
