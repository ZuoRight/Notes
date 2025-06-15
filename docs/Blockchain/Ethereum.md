# Ethereum

[以太坊历史](https://ethereum.org/en/history/)

[官方文档](https://ethereum.org/en/developers/docs/intro-to-ethereum/)

以太坊由 Vitalik Buterin 与 Gavin Wood 等人联合发起

> 维塔利克·布特林，俗称 V 神，94年，俄裔加拿大程序员，写了以太坊的[白皮书](https://ethereum.org/zh/whitepaper/)
>
> Gavin Wood，英国人，80年，计算机科学家，以太坊基金的第一位 CTO，写了以太坊的[黄皮书](https://github.com/ethereum/yellowpaper)（[中文版](https://github.com/yuange1024/ethereum_yellowpaper/blob/master/ethereum_yellow_paper_cn.pdf)），提出并参与开发了 EVM 的编程语言 Solidity，还开发过以太坊的 Rust 版客户端 Parity，2016 年离开以太坊基金会，后联合其他人创办了另一个底层公链 Polkadot，并为其写了白皮书。

以太坊的编程语言与 Bitcoin 的 Script 不同，使用了图灵完备的 Solidity 来编写所谓的「智能合约」，引发了公链生态，虽然他自己一开始也并没有想到

> 智能合约的概念最初是由 Nick Szabo（尼克·萨博）于 1997 年提出的，V 神后来觉得不应该用这个名字
>
> ![20220729195329](https://image.zuoright.com/20220729195329.png)

以太坊采用常见的普通账户模型，也叫账户余额模型（Account-Balance），相对更自由

参考：<https://time.geekbang.org/column/article/6511>

账户拥有4个参数：`{nonce, balance, codeHash, StorageRoot}`

与 Bitcoin 不同，Ethereum 中的 Nonce 表示交易递增的序号（从 0 开始，每个账户独立维护自己的 Nonce），用于防止双花和重放攻击，钱包通常自动处理，如果 Nonce 不连续，交易会被卡住，需手动替换或重置。

## 节点

以太坊是一个由分布式节点构成的去中心化计算机网络

> [节点实时分布图](https://etherscan.io/nodetracker)

节点分类

- Full node(全节点)：存储完整的区块链数据，通常用于挖矿
- Light node(轻节点)：只存储区块头，目前以太坊还不支持大量的轻节点，对应的客户端也几乎没有
- Archive node(存档节点)：通常用于区块链浏览器和钱包服务等
- Simulated node(模拟节点)：用于本地开发测试

客户端是节点的一种实现，Ethereum 1.0 只需要运行一个执行客户端，其中 Go 语言编写的 Geth 是最早也是使用最多的客户端

![20220727154624](https://image.zuoright.com/20220727154624.png)

Ethereum 2.0 需要再运行一个共识客户端

![20220727155848](https://image.zuoright.com/20220727155848.png)

升级后交互示意图

![20220727154452](https://image.zuoright.com/20220727154452.png)

## 区块

- <https://ethos.dev/beacon-chain>
- <https://stonecoldpat.substack.com/p/how-proof-of-stake-ethereum-works>

Ethereum 2.0(The Beacon Chain) 采用了权益证明共识机制（PoS, Proof of Stake），向存储合约质押（Staking）32 个 ETH 成为验证节点（Validators，验证者）

Validators 参与出块获得奖励或惩罚（Staking Rewards and Penalties）会影响质押的 ETH 数量变化，当小于 16 ETH 时将会被取消验证者资格

> Lido 是基于 Beacon Chain 上建造的 Dapp，用户不需要锁定 ETH，也不需要达到 32ETH 的这一门槛即可参与质押挖矿，并且用户将 ETH 质押到 Lido 合约中会获得等量的 stETH，可用于交易、Swap、借贷等 DeFi

比特币主要用于支付，需要更多的安全确认，所以限制平均 10min 出一个块

而以太坊则是执行智能合约为主，支付功能并不是主要功能，所以平均每 12 秒出一个块

- 12s 称为一个时间间隙（time slot），即 $1slot = 12s$
- 32 个 slot 称作一个纪元（epoch）即 $1epoch = 32slot = 384s$ (6 min 24 s)

![20230507202055](https://image.zuoright.com/20230507202055.png)

每个 epoch 会有 32 个 Validators 参与权益证明协议，这些节点组成一个验证者委员会，出于安全目的，Validators 在每个 epoch 开始时通过混洗协议(swap-or-not)被重新随机分配到委员会中，委员会中还有一个额外的角色叫做聚合者（自愿）

每个 slot 会产生一个新的区块

- 前 4s：通过伪随机协议（RANDAO）分配一个验证者（Validators）作为区块提议者（Proposer）向委员会提议一个区块
- 中 4s：其它委员会节点作为见证者（Attester）检查区块并进行 FFG 投票
- 后 4s：FFG 投票由聚合者节点聚合，进行 LMD GHOST 投票，广播给下一个 slot 的 Proposer

如果轮到某个 Validators 提议区块时但刚好不在线，则 slot 可能为空

![20230508112413](https://image.zuoright.com/20230508112413.png)

虽然每个节点在一个 epoch 只能投一票，但不同节点质押的 ETH 余额不同，所以投票的权重不同，若有冲突区块会导致链分叉，则累积质押 ETH 最多的链被称之为规范链(canonical chain)

### 区块状态

<https://info.etherscan.com/epoch-in-ethereum/>

- `Unfinalized` 尚未被 Validators 构建和验证的区块，可能会被重组
- `Unfinalized(Safe)` 已被 $2/3$ 的 Validators 验证的区块，重组可能性较小
- `Finalized` 已由 Validators 完全验证的区块，不太可能被重组

### 区块内容

<https://ethereum.org/zh/developers/docs/blocks/>

```text
slot：区块所属的时隙
proposer_index：提出区块的 Validators 的 ID
parent_root：前一个区块的哈希
state_root：状态对象的根哈希
body：包含一些字段的对象，定义如下
    randao_reveal：用于选择下一个区块提议者的值
    eth1_data：有关存款合约的信息
    graffiti：用于标记区块的任意数据
    proposer_slashings：将要受到惩罚的验证者的列表
    attester_slashings：将要受到惩罚的验证者的列表
    attestations：支持当前区块的认证列表
        aggregation_bits：参与此认证的验证者列表
        signature：所有证明验证者的聚合签名
        data：具有多个子字段的容器
            slot：认证涉及的时隙
            index：证明验证者的索引
            beacon_block_root：包含此对象的信标链区块的根哈希
            source：上一个合理的检查点
            target：最新的时段边界区块
    deposits：存入存款合约中的新存款的列表
    voluntary_exits：将要退出网络的验证者的列表
    sync_aggregate：用于服务轻客户端的验证者子集
    execution_payload：由执行客户端传送的交易
        parent_hash：父块的哈希
        fee_recipient：接收交易费的帐户地址
        state_root：应用此区块中的变化后全局状态的根哈希
        receipts_root：交易收据树的哈希
        logs_bloom：包含事件日志的数据结构
        prev_randao：随机选择验证者时使用的值
        block_number：当前区块的编号
        gas_limit：此区块允许使用的最大燃料量
        gas_used：此区块实际使用的燃料量
        timestamp：出块时间
        extra_data：作为原始字节的任意附加数据
        base_fee_per_gas：基础费的值
        block_hash：执行区块的哈希
        transactions：要执行交易的列表
```

### 区块奖励

区块奖励通常包含：`Static Block Reward + Priority Fees`

- old: 每 210,000 个区块 Static Block Reward 减半一次，最初为 5 ETH  
- new: ETH 2.0 staking rewards

除了基本的奖励之外，Validators 能够从他们生产的区块内重新排序交易来获得额外的收益，这部分收益称之为 MEV(Maximal Extractable Value)

<https://info.etherscan.com/exploring-the-world-of-mev/>

## Transaction Fee

- [EIP-1559](https://eips.ethereum.org/EIPS/eip-1559) ([video](https://www.youtube.com/watch?v=MGemhK9t44Q&ab_channel=Finematics))
- [Gas 构成](https://medium.com/@b1995/gas-%E7%9A%84%E7%B2%BE%E5%8D%8E%E6%80%BB%E6%95%B4%E7%90%86-8aa579fcde8c)

所有会改变智能合约状态的操作都会消耗 Gas，Gas 的基本单位为 Gwei

一笔交易可能包含各种操作，不同操作消耗不同，所以不同种类的交易 `Gas Used` 也不同

![20230508162738](https://image.zuoright.com/20230508162738.png)

交易时需要设置一个 `Gas Limit`，用于限制 Gas 的最高消耗量（最小可设置为 `21000`），实际消耗的为 `Usage_by_Txn`

EIP-1599 之前，即传统方式(Legacy)，`Gas Fees = Fees * Gas Limit`，其中 `Fees` 的多少取决于用户愿意支付多少，但矿工会选择多的优先打包，因此产生竞价，波动较大，且难以预估

EIP-1599 之后，即 2021.8 伦敦升级后的新方式，将 Fees 改为了 `Base Fee + Priority Fee`

其中 `Priority Fee` 是给矿工的小费，可以设置一个最大值，矿工会优先打包小费高的

`Base Fee` 最终会被燃烧掉（Burnt），大小是由当前网络拥堵程度动态决定的

> 一个区块的最大容量为 25M 个 Gas，如果当前区块容量大于 50%，即大于 12.5M，则下一个区块的 Base Fee 价格上调 12.5%，反之下调

```text
单价：Gas_Price ≤ Base Fee + Max_Priority Fee
总价：Transaction_Fee = Gas_Price * Usage_by_Txn
Burnt = Base * Usage_by_Txn
```

![20230508163143](https://image.zuoright.com/20230508163143.png)

在交易时需要设置一个 `Max Fee`，剩余的会退还回来：`Savings Fees = Max Fee - Base Fee - Max Priority Fee`

> 之所以叫 Savings Fees 大概是因为传统方式时的 Fees 用于合约操作剩下的都会被当作矿工小费，不会退还

![20230508223644](https://image.zuoright.com/20230508223644.png)

- ETH 转账

![ETH转账](https://image.zuoright.com/20230508165817.png)

- ERC20-USDT 转账

![ERC20-USDT转账](https://image.zuoright.com/20230508165920.png)

### 单位

计算机处理浮点数通常会有误差，所以一般涉及到金钱相关的会缩小单位，比如 1 角应该存储为 10 分，而不是 0.1 元

以太坊中常用单位：$1 Ether = 10^9 Gwei = 10^{18} wei$

- Gwei 主要用于表示 Gas Fees
- wei 是以太坊中的最小单位，以 b-money 的发明者 Wei Dai 命名，常用于合约开发中

![20230508162327](https://image.zuoright.com/20230508162327.png)

在线单位换算工具

- <https://etherscan.io/unitconverter>
- <https://eth-converter.com/>

### Gas 优化

- 尽量使用常量或不变量
- 尽量不要存储在内存中
- 初始化之后不允许改变
- 用 `if (...) revert xxerror();` 替代 `require();` 语句

```js
// require(msg.sender == i_owner, "Sender is not owner");

// 比如在合约外自定义错误：error NotOwner(); 然后
if (msg.sender != i_owner) revert NotOwner();
```
