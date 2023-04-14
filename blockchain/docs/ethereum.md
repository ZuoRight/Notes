# Ethereum

[以太坊历史](https://ethereum.org/en/history/)

[官方文档](https://ethereum.org/en/developers/docs/intro-to-ethereum/)

以太坊由俄裔加拿大程序员Vitalik Buterin（维塔利克·布特林，俗称V神，94年）与 Gavin Wood（英国人，计算机科学家，80年） 等人联合发起

V神写了以太坊的[白皮书](https://ethereum.org/zh/whitepaper/)

Gavin Wood 写了以太坊的[黄皮书](https://github.com/ethereum/yellowpaper)（[中文版](https://github.com/yuange1024/ethereum_yellowpaper/blob/master/ethereum_yellow_paper_cn.pdf)），是以太坊基金的第一位CTO，提出并参与开发了EVM的编程语言Solidity，2016年离开以太坊基金，后来还开发了Rust编写了以太坊的客户端Parity，后又联合创办了另一个底层公链Polkadot，并为其写了白皮书。

以太坊将交易的编程语言从Script改为了图灵完备的Solidity，称之为智能合约，引发了公链生态，虽然他自己一开始也并没有想到

![20220729195329](http://image.zuoright.com/20220729195329.png)

## 单位

计算机处理浮点数通常会有误差，所以一般涉及到金钱相关的会缩小单位，比如1角应该存储为10分，而不是0.1元

以太坊中常用单位：`1 Ether` = `10^9 Gwei` = `10^18 wei`

- wei 是以太坊中的最小单位（以b-money的发明者 Wei Dai 命名），常用于开发中
- Gwei 主要用于表示Gas费

## 挖矿

以太坊发行总量没有上限，但交易过程中会不断地销毁

- 关于区块的大小限制

比特币一个区块大小是1MB，里面可以包含1500到2000笔交易。

而以太坊区块会根据运行在上面的智能合约的复杂程度决定，区块目标大小为1500万个gas，最高3000万个gas，所以一个区块的总交易数量为：`gas总数/21000`，理论上最高不过1428笔，如果当前区块超过1500万个gas，则下一个区块会提高Base fee，以此降低交易数量（因为手续费增加，理论上交易的人就会相对变少）。

- 关于区块的产出速率

比特币主要用于支付，需要更多的安全确认，所以限制平均10min出一个块。

而以太坊则是执行智能合约为主，支付功能并不是以太坊的主要功能，平均13s左右。

## 节点

以太坊是一个由分布式节点构成的去中心化计算机网络，[节点实时分布图](https://etherscan.io/nodetracker)

节点分类

- Full node(全节点)：存储完整的区块链数据，通常用于挖矿
- Light node(轻节点)：只存储区块头，目前以太坊还不支持大量的轻节点，对应的客户端也几乎没有
- Archive node(存档节点)：通常用于区块链浏览器和钱包服务等
- Simulated node(模拟节点)：用于本地开发测试

客户端是节点的一种实现，以太坊升级前(Eth1.0)只需要运行一个执行客户端(execution clients)，其中Go语言编写的Geth是最早也是使用最多的客户端

![20220727154624](http://image.zuoright.com/20220727154624.png)

升级后(Eth2.0)需要再运行一个共识客户端(consensus clients)

![20220727155848](http://image.zuoright.com/20220727155848.png)

升级后交互示意图

![20220727154452](http://image.zuoright.com/20220727154452.png)
