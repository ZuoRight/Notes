# 账户模型

> [bitcoindeveloper·transactions](https://developer.bitcoin.org/devguide/transactions.html)  
> [廖雪峰区块链教程：P2P交易原理、可编程支付原理](https://www.liaoxuefeng.com/wiki/1207298049439968/1311929802948641)

BTC 采用 UTXO 模型，Unspent TX Output，TX指交易(Transaction)，就是还没有被使用的交易输出，可以理解为就是余额

比特币使用 LevelDB 存储 UTXO，比特币的交易就是不断地消耗现有的 UTXO，并产生新的 UTXO

与传统的账户体系一对一不同，它类似于保险箱，A 将资产锁进保险箱中，只有B可以打开，是多对多的输入和输出关系（Coinbase交易除外，只有一个输入）

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

## 地址类型

![20230509234516](http://image.zuoright.com/20230509234516.png)

比特币地址是交叉兼容的，每种类型的地址都可以给任意类型的地址发送资金，但是手续费不同：`原生隔离见证地址（bc1开头） < 隔离见证兼容地址（部分3开头） < 传统地址（1开头） < 多签地址（部分3开头）`

1. 隔离见证兼容地址转账手续费比传统地址节省 24%
2. 原生隔离见证地址转账手续费比传统地址节省 35%
3. 隔离见证地址转账手续费比多签地址最多可以节省 70%

### P2PKH

Pay to PubKey Hash 付款至公钥哈希，最初的传统地址（Legacy）

`Base58(0x00 + Hash160(Public Key) + Checksum)`

编码后 1 开头，34个字符，不包含 0、O、l、I 等容易混淆的字符，比如：`1MbeQFmHo9b69kCfFa6yBr7BQX4NzJFQq9`

假设Alice给Bob转账，相当于将资产放入保险箱，用Bob的公钥加密，Bob只有在向他人转账的时候，才会使用私钥签名打开保险箱，然后将其锁入另一个收款方的保险箱

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
- 隔离见证兼容地址(Nested SegWit)：P2SH-P2WPKH

`Base58(0x05 + Hash160(Script) + Checksum)`

编码后 3 开头，34个字符，比如：`3EmUH8Uh9EXE7axgyAeBsCc2vdUdKkDqWK`

### P2WPKH / Bech32

专为隔离见证设计的格式，2017 年底由 BIP173 定义

原生隔离见证地址（Native SegWit）

Base32 编码，bc1 开头，42个字符，不区分大小写，只包含数字和小写字母：0～9，a~z，比如：`bc1qj89046x7zv6pm4n00qgqp505nvljnfp6xfznyw`

QR 码更小，更容易防错，更安全，更高效，手续费更低

### P2TR / Taproot
