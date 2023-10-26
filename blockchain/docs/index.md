---
hide:
  - footer
  - feedback
---
# 引言

区块链可以理解为是一个全球共享的点对点去中心化且不可篡改的事务性数据库，在分布式网络节点之间更新和共享。

涉及P2P网络协议，分布式一致性算法，加密签名算法，账户与存储模型等。

使用区块链发行的货币，被称之为加密货币(Crypto Token)，也可称之为通证。

对于比特币，区块链是一个公开账本，而对于以太坊它是一个智能合约。

三要素

- 加密算法
- 共识机制
- 经济模型

## 共识机制

- PoW(Proof of Work) 工作量证明
- PoS(Proof of Stake) 权益证明

## 区块链可视化

- <https://andersbrownworth.com/blockchain/>
- <https://txstreet.com/v/eth-btc>
- <http://ethviewer.live/>

![20230429200042](http://image.zuoright.com/20230429200042.png)

## 区块浏览器

从技术上看，区块浏览器的主要工作就是把区块扫描到数据库服务器中，然后搭建一个 Web 访问服务，用户只需要输入交易哈希或者区块哈希，即可查询到交易是否已经被打包和确认。

![20231026220750](https://image.zuoright.com/20231026220750.png)

多链：[OKLink](https://www.oklink.com/all-chain)

- <https://www.oklink.com/btc>
- <https://www.oklink.com/eth>

Ethereum：[Etherscan](https://etherscan.io/)

- [Blocks List](https://info.etherscan.com/exploring-block-page/)
- [Block Detail](https://info.etherscan.com/exploring-block-details-page/)
- [Transaction](https://info.etherscan.com/understanding-an-ethereum-transaction/)

## 提案

加密货币钱包相关的技术实现规范主要来自于提案

- 比特币改进提案：[BIP（Bitcoin Improvement Proposal）](https://github.com/bitcoin/bips/tree/master)
- 以太坊改进提案：EIP（Ethereum Imporvement Proposals）
- 以太坊意见征求稿：ERC（Ethereum Request For Comment）

## 可扩展性

由于公链生态爆发，所有交易都在链上处理，导致拥堵处理速度越来越慢，手续费越来越高，引发了比特币扩容之争导致软分叉、硬分叉，促使以太坊升级分层，引出跨链，以及各种新公链

![20220729185819](http://image.zuoright.com/20220729185819.png)

跨链项目

- Cosmos（ATOM）
- Polkadot（DOT）
