# Bitcoin

比特币是最早被创造出来的去中心化加密货币，诞生于 2008 年世界金融危机之时

- 2008.8.18 `Satoshi Nakamoto`(中本聪) 和 `Martti Malmi`(马尔·蒂马尔米) 注册了域名：[bitcoin.org](https://bitcoin.org)（准官网）
- 2008.11.1 中本聪发表了比特币的白皮书：《Bitcoin: A Peer-to-Peer Electronic Cash System》（比特币：一种点对点的电子现金系统）
- 2008.11.9 比特币项目在 sourceforge.net 平台注册
- 2009.1.3 中本聪打包出了创始区块（`Genesis Block`）

![20220718221933](https://image.zuoright.com/20220718221933.png)

> 创始区块中附带了一段文本：  
> The Times 03/Jan/2009 Chancellor on brink of second bailout for banks  
> 《泰晤士报》 2009年1月3日 英国财政大臣正欲对银行业实施第二轮救助

![20220718221945](https://image.zuoright.com/20220718221945.png)

- 2009.1.9 中本聪在 sourceforge.net 发布了比特币 `0.01` 版本源码
- 2010.5.22 拉斯诺用挖到的比特币购买了两个比萨，共花费 10000 BTC，这是比特币第一次被用于实物支付
- 2010.7 世界上第一家比特币交易所 Mt.Gox(俗称门头沟) 在日本东京成立
- 2010.12.12 中本聪在 bitcointalk.org 论坛发布了比特币 `0.3.19` 版本公告，然后便销声匿迹，此时比特币价格约 `$0.25`
- 2014.3.7 中本聪在 p2pfoundation 论坛发布了一条辟谣消息：我不是多里安·中本聪，此后便再也没出现过

## 节点

- 全节点：存储和处理所有区块数据的节点，对应的钱包服务器要先把整个区块扫描一遍建立 address -> utxo 查询表，以后每发现一个新区块就做增量更新
- 轻节点：简化支付确认协议（SPV, Simplified Payment Verification）只下载区块头，使用区块头确认工作量证明，只下载与其交易相关的默克尔树的分支

## 区块

比特币的区块链可以看作是运行在 P2P 网络中的公开账本，一笔笔交易存储在区块结构体中，然后被打包成一个区块，一个个区块按更新顺序串联成一条区块链，第一个区块被称之为创世区块。

每个区块由区块头和区块体两部分组成

![20220721173614](https://image.zuoright.com/20220721173614.png)

打包区块的过程叫做工作量证明（PoW, Proof of Work），因为可以从中获取奖励，所以俗称挖矿，由分布式网络中的节点来完成，俗称矿工，用户购买硬件成为矿工。

![20220721193416](https://image.zuoright.com/20220721193416.png)

交易按顺序存储在区块体中，比特币诞生之初，单个区块最大可到 32M，但由于有人恶意制造的大量小额转账使网络中有大量的待确认交易，影响了网络运转，所以改为了 1M（大约 1500～2000 笔交易）

![20231027211035](https://image.zuoright.com/20231027211035.png)

> 示例参考：<https://andersbrownworth.com/blockchain/public-private-keys/blockchain>

第一条交易是矿工写给自己的挖矿奖励，称作 Coinbase 交易，然后才是普通用户的交易。

当然交易可以只有 Coinbase，即空块，比如创世区块，但通常矿工不会这样做，因为打包其他交易还可以赚取手续费。

矿工在写奖励时不能乱写，否则会验证失败，白费精力，不过矿工可以优先选择打包手续费高的交易

矿工每挖取并成功更新一个区块，获取的激励为：`区块奖励` + `交易手续费`，正因如此，才会有人愿意主动去做这件事，比特币网络才得以正常运行。

但挖矿并没有那么容易，需要满足一定的条件

首先将每一笔交易哈希，然后每两个交易的哈希再哈希，依次递归，如果遇到奇数则使用自身的副本配对，最终会形成一颗倒立的哈希二叉树，其根哈希被称作「默克尔根」(Merkle)。

> 哈希二叉树，是一种用作快速归纳和校验大规模数据完整性的数据结构，在比特币网络中，可以通过默克尔树快速地校验某个交易是否存在于某个区块中。

默克尔根与父区块的哈希等组成区块头

- 父区块的哈希
- 默克尔根
- Nonce（随机数）
- 其它：区块高度、bits(难度系数)、时间戳等

然后将区块头经过两次哈希，如果生成的哈希值小于某个目标值，便可以广播到区块链网络，反之则需要重新哈希。

> 中本聪在设计比特币时借鉴了 Hashcash（反垃圾邮件系统）的双哈希模式：`SHA-256(SHA-256(区块头)) < 目标值`

目标值是基于难度系数根据公式动态算出来的，由 256bits 二进制数组成，每一位不是 0 就是 1，0 的概率为 $1/2$，要求小于目标值，实际就是要求前 n 位有多少个 0，即概率为 ${1/2}^n$，理论上目标值越小则需要 0 越多，概率越低，挖矿的难度越大。

为了保证生成一个区块的时间恒定，挖矿难度每过一个固定周期（2016个区块）就会调整一次，称之为难度调整周期（Difficulty Adjustment Period）

计算一下上一个周期的平均耗时，理想时间为两周（1,209,600秒，平均 10 分钟一个区块），如果过快则会调高难度（调小目标值），反之调低。

观察区块头可知，除了 Nonce 和时间戳外其它都是不能随意改变的，其中时间戳并不需要时时改变，只要大于上一个区块的时间戳并且在将来 2 小时以内即可。所以通常只能用穷举法不断地调换随机数来满足目标条件，这个过程便称之为工作量证明。

> 不同矿工打包时的 Coinbase 交易收取地址不同，而且打包的交易也不一定相同，所以每个矿工要寻找的随机数也是不同的。

比特币网络的算力和难度一直呈上升趋势，目前全网算力大概 200EH/s 左右，而单机 CPU 算力不过几 M，GPU 算力也不过 1G，所以单机挖矿的成功率几乎等于 0，现在主要是专用的 ASIC 芯片构建的矿池挖矿。

> 算力：每秒产生哈希碰撞的能力，最小单位：H(Hash)，即 1 次运算  
> $1E=1000P=1000,000T=1000,000,000G=1000,000,000,000K=1000,000,000G=1000,000,000,000,000H$

平均 10 分钟左右挖出一个区块，1 小时就是 6 个，大致可以推算出比特币的总量约为 2100 万个，预计在 2140 年全部挖出。

> $6*24*365*4 * 50(1+1/2+1/4+1/8+...) = 21,000,000$

区块奖励最初为 50 个 BTC，每开采 210,000 个区块则会减半，2012 年首次减半到 25 个，2016 年减半到 12.5 个，2020 年 5 月减半到了 6.25 个，平均每四年减半一次

> 计算公式：`(Halving/Reduction block – Next block height) × Average block time – Estimated time for the next`
>
> [减半倒计时 Reduction & Halving Countdown](https://www.oklink.com/halving)

![BTC Halving Countdown](https://image.zuoright.com/BTC%20Halving%20Countdown.png)

## UTXO

- [bitcoindeveloper·transactions](https://developer.bitcoin.org/devguide/transactions.html)
- [廖雪峰区块链教程：P2P交易原理、可编程支付原理](https://www.liaoxuefeng.com/wiki/1207298049439968/1311929802948641)

BTC 采用 UTXO 账户模型，Unspent TX Output，TX 指交易(Transaction)，就是还没有被使用的交易输出，类似余额

比特币使用 LevelDB 存储 UTXO，比特币的交易就是不断地消耗现有的 UTXO，并产生新的 UTXO

与常见的一对一传统账户体系不同，它类似于保险箱，A 将资产锁进保险箱中，只有 B 可以打开，是多对多的输入和输出关系（Coinbase 交易除外，只有一个输入）

> 比特币的交易最小单位为聪(Satoshi)，$1 BTC = 10^8 Sats$（1 亿聪）

![20220721233301](https://image.zuoright.com/20220721233301.png)

比如忽略手续费的前提下，A 有 10 BTC，全部转给 B，然后 B 把 10 BTC 又全部转给我了 C，这就是 1 输入 1 输出

如果 B 只转 5 BTC 给 C，剩下的 5 BTC 需要转给自己，这就是 1 输入 2 输出，如果此时再有个人给 C 转 5 BTC，那 C 的这 10 BTC 就对应两个输入

无论输入还是输出实际上都是一个脚本，输出是一个锁定脚本，输入是一个解锁脚本，比特币的脚本语言是一种类似 FORTH 的基于栈结构的低级语言，为了安全特意被设计为无状态且非图灵完备。

![20220721233710](https://image.zuoright.com/20220721233710.png)

```text
FROM: UTXO Hash#index
AMOUNT: 0.5 btc
TO: OP_DUP OP_HASH160 <PubkeyHash> OP_EQUALVERIFY OP_CHECKSIG
```

![20220722103520](https://image.zuoright.com/20220722103520.png)

```text
           Sig 48 (30450221...68fa9b01)
        PubKey 21 (03dd8763...14cf740f)
        OP_DUP 76
    OP_HASH160 a9
    PubkeyHash 14 (dc5dc65c...fe9f489c)
OP_EQUALVERIFY 88
   OP_CHECKSIG ac
```

![20220722103438](https://image.zuoright.com/20220722103438.png)

每一笔交易都有一个唯一的 TXID，可以在区块链浏览器中查询交易相关的信息

## 参考

- Wikipedia：[比特币历史](https://zh.wikipedia.org/wiki/%E6%AF%94%E7%89%B9%E5%B9%A3%E6%AD%B7%E5%8F%B2)
- [区块链启示录：中本聪文集](https://book.douban.com/subject/30338899/)（The Book Of Satoshi: The Collected Writings of Bitcoin Creator Satoshi Nakamoto）（[读后感](https://mirror.xyz/wtfacademy.eth/Jezo3dZ_9IJ6yclzT_M21Y_njZaohfy9obiPRlemy8s)）
- [精通比特币](https://wizardforcel.gitbooks.io/masterbitcoin2cn/content/)
- 各种语言的白皮书：<https://bitcoin.org/en/bitcoin-paper> （[李笑来翻译的中英对照版](https://lixiaolai.com/#/bitcoin-whitepaper-cn-en-translation/Bitcoin-Whitepaper-EN-CN.html)）
- [以太坊白皮书](https://ethereum.org/zh/whitepaper/)
- [比特币准官网的开发者指南](https://developer.bitcoin.org/devguide/block_chain.html)
- 最新源码：<https://github.com/bitcoin>
