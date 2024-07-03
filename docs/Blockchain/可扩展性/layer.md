# 以太坊分层

根据区块链不可能三角原理，在主网扩容会牺牲掉去中心化程度和安全性，我们知道计算机科学领域的任何问题都可以通过增加一个间接的中间层来解决，于是便引出了 Layer2、侧链、跨链等技术来解决这个问题。

Layer1 和 Layer2 的关系，有点像个人电脑时代里的 CPU 硬件和操作系统。layer1（链上）是不同架构的 CPU，layer2（链下）是操作系统。可以把计算、交易等业务处理拿到主链之外来执行，只在主链上反映最终的结果，中间过程不在主链做记录。

链上的生态基本上就是各种各样的公链，以共识和安全为主，为链下生态打基础就好，不过链上扩容也是非常重要的，可以帮助链下的扩展。

大部分 Layer2 是基于以太坊的，如果是其它异构链的 Layer2 可能需要先完善 Layer1 的工具，并且考虑开发者的学习成本，可能需要兼容 EVM 等。

Layer2 的几种解决方式

![20220731183253](http://image.zuoright.com/20220731183253.png)

Rollup 是将运算移到链下，数据留在链上，安全性依赖底层公链，其它几种是将数据与运算都放在链下处理，会有中心化的问题

> 图片参考：<https://twitter.com/321bigcat/status/1553004159208529920>

![20220731183608](http://image.zuoright.com/20220731183608.png)

## State Channel（状态通道）

State Channel 类似于预存，可以提前预存一笔款项到智能合约，每次交易买方只需要支付一张支票给卖方，到一定期限，卖方将支票拿到线上统一做一次结算，整体只扣一次手续费，如果之前预存有剩余，将退还给买方，适用于小额频繁交易场景，与比特币的闪电网络比较像

## Plasma

Plasma 类似于团购，不同的买方将钱支付给某个中间商，中间商汇集大家的交易，然后一起上链，缺点是上链周期不确定，而且提现需要做欺诈验证，类似冻结期，可能七天后才能到账。

## Validium

Validium 由 Starkware 团队在 2020 年 6 月份才提出，跟 Plasma 一样数据和状态都在链下处理，但是用到了零知识证明来保证更安全和去中心化，不过跟 ZK Rollup 一样实用场景比较有限，目前主要用在去中心化交易所 StarkEx 上面

## Rollup

### Optimistic Rollup

Optimistic Rollup 有用到 Plasma 的欺诈验证技术，提现也比较慢，应用项目主要有：`Optimism` 和 `Arbitrium`

两者区别在于前者欺诈验证是单轮制，而后者是多轮制的，此外 Optimism 要求开发者必须用特定版本的 Solidity 开发，而 Arbitrium 更加包容，只要是 EVM 兼容的语言都可以。

### ZK Rollup（零知识汇总）

ZK Rollup（Zero Knowledge Rollup）利用零知识证明(ZKP)交易与取现都比较快，但是手续费相对较高一些，而且实现难度也更大，目前还处在理论阶段。

ZK 证明系统

- ZK-SNARK（Zero-Knowledge Succinct Non-Interactive Argument of Knowledge）简洁的非交互式论证
- ZK-STARK（Zero-Knowledge Scalable Transparent Argument of Knowledge）可扩展的透明知识论证

Loopring 是第一个实现 ZK Rollup 的团队，采用了 ZK-SNARK。

MatterLabs 团队基于 SNARK 开发了 zkSync。

StarkWare 团队基于 STARK 开发了 StarkEx 和 StarkNet。

> 其技术团队的负责人之一是 Zcash 的联合创始人 Alessandro Chiesa

使用 StarkEx 的有 dYdX、DiversiFi、Immutable X 和 Sorare，不过 StarkEx 上的项目彼此独立，无法组合。可能是因为这个原因，StarkWare 又推出了开放性更好的 StarkNet。

StarkNet 是真正意义上的 Layer2，任何 DAPP 都可以在上面部署，应用之间可组合。
