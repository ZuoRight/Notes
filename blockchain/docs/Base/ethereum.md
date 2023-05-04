# Ethereum

以太坊发行总量没有上限，但交易过程中会不断地销毁

- 关于区块的大小限制

比特币一个区块大小是1MB，里面可以包含1500到2000笔交易。

而以太坊区块会根据运行在上面的智能合约的复杂程度决定，区块目标大小为1500万个gas，最高3000万个gas，所以一个区块的总交易数量为：`gas总数/21000`，理论上最高不过1428笔，如果当前区块超过1500万个gas，则下一个区块会提高Base fee，以此降低交易数量（因为手续费增加，理论上交易的人就会相对变少）。

- 关于区块的产出速率

比特币主要用于支付，需要更多的安全确认，所以限制平均10min出一个块。

而以太坊则是执行智能合约为主，支付功能并不是以太坊的主要功能，平均13s左右。

## Epoch

<https://info.etherscan.com/epoch-in-ethereum/>

类似比特币中的难度调整周期，以太坊引入了 Epoch 的概念，每个 Epoch 固定为 10000 个区块，除了调整难度目标(Difficulty Target)外，还会计算区块奖励和交易手续费等。

commitment levels 或 status of the block

- `Unfinalized`
- `Unfinalized(Safe)`
- `Finalized`

## MEV

<https://info.etherscan.com/exploring-the-world-of-mev/>
