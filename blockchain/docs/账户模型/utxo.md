# UTXO

> [bitcoindeveloper·transactions](https://developer.bitcoin.org/devguide/transactions.html)  
> [廖雪峰区块链教程：P2P交易原理、可编程支付原理](https://www.liaoxuefeng.com/wiki/1207298049439968/1311929802948641)

BTC 采用 UTXO 模型，Unspent TX Output，TX指交易(Transaction)，就是还没有被使用的交易输出，可以理解为就是余额

比特币使用 LevelDB 存储 UTXO，比特币的交易就是不断地消耗现有的 UTXO，并产生新的 UTXO

与常见的一对一传统账户体系不同，它类似于保险箱，A 将资产锁进保险箱中，只有B可以打开，是多对多的输入和输出关系（Coinbase交易除外，只有一个输入）

比如忽略手续费的前提下，小A有10BTC，全部转给小B，然后小B把10BTC又全部转给我了小C，这就是1输入1输出

如果小B只转5BTC给小C，剩下的5BTC需要转给自己（UTXO），这就是1输入2输出，如果此时再有个人给小C转5BTC，那小C的这10BTC就对应两个输入

![20220721233301](http://image.zuoright.com/20220721233301.png)

无论输入还是输出实际上都是一个脚本，输出是一个锁定脚本，输入是一个解锁脚本，比特币的脚本语言是一种类似FORTH的基于栈结构的低级语言，为了安全特意被设计为无状态且非图灵完备。

![20220721233710](http://image.zuoright.com/20220721233710.png)

```text
FROM: UTXO Hash#index
AMOUNT: 0.5 btc
TO: OP_DUP OP_HASH160 <PubkeyHash> OP_EQUALVERIFY OP_CHECKSIG
```

![20220722103520](http://image.zuoright.com/20220722103520.png)

```text
           Sig 48 (30450221...68fa9b01)
        PubKey 21 (03dd8763...14cf740f)
        OP_DUP 76
    OP_HASH160 a9
    PubkeyHash 14 (dc5dc65c...fe9f489c)
OP_EQUALVERIFY 88
   OP_CHECKSIG ac
```

![20220722103438](http://image.zuoright.com/20220722103438.png)

每一笔交易都有一个唯一的TXID，可以在区块链浏览器中查询交易相关的信息

## 节点

- 全节点：存储和处理所有区块数据的节点，对应的钱包服务器要先把整个区块扫描一遍建立address->utxo查询表，以后每发现一个新区块就做增量更新
- 轻节点：简化支付确认协议（SPV, Simplified Payment Verification）只下载区块头，使用区块头确认工作量证明，只下载与其交易相关的默克尔树的分支
