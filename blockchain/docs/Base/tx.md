# Transactions

<https://info.etherscan.com/understanding-an-ethereum-transaction/>

## Gas

Gas Price 报价：<https://www.oklink.com/cn/eth/gas-price/price>

## 单位

单位换算：<https://eth-converter.com/>

计算机处理浮点数通常会有误差，所以一般涉及到金钱相关的会缩小单位，比如1角应该存储为10分，而不是0.1元

以太坊中常用单位：`1 Ether` = `10^9 Gwei` = `10^18 wei`

- wei 是以太坊中的最小单位（以b-money的发明者 Wei Dai 命名），常用于开发中
- Gwei 主要用于表示Gas费

## 比特币

> 参考  
> [廖雪峰区块链教程：P2P交易原理、可编程支付原理](https://www.liaoxuefeng.com/wiki/1207298049439968/1311929802948641)  
> [bitcoindeveloper·transactions](https://developer.bitcoin.org/devguide/transactions.html)

区块体中的每一笔交易并不是平时我们见到的那种付款方和收款方一对一的关系，而是由多对多的输入和输出构成，称之为UTXO模型，除了Coinbase交易外（只有一个输出），其它交易至少有一个输入和一个输出。

> Unspent TX Output，TX指交易(Transaction)，就是还没有被使用的交易输出，可以理解为就是余额
>
> 比特币使用LevelDB存储UTXO，比特币的交易就是不断地消耗现有的UTXO，并产生新的UTXO

![20220721233301](http://image.zuoright.com/20220721233301.png)

忽略手续费的前提下，比如小A有10BTC，全部转给小B，然后小B把10BTC又全部转给我了小C，这就是1输入1输出；如果小B只转5BTC给小C，剩下的5BTC需要转给自己（UTXO），这就是1输入2输出，如果此时再有个人给小C转5BTC，那小C的这10BTC就对应两个输入。

无论输入还是输出实际上都是一个脚本，输出是一个锁定脚本，输入是一个解锁脚本，比特币的脚本语言是一种类似FORTH的基于栈结构的低级语言，为了安全特意被设计为无状态且非图灵完备。

> 脚本有P2PKH(Pay to Public Key Hash)和P2SH(Pay To Script Hash)等不同类型

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

比特币的交易最小单位为聪(Satoshi)，`1btc=10^8聪`（1亿聪）
