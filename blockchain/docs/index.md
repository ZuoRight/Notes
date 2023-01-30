---
hide:
  - footer
  - feedback
---
# 引言

比特币是最早被创造出来的一种去中心化的加密货币，其所依赖的技术被称之为区块链，涉及P2P网络协议，分布式一致性算法，加密签名算法，账户与存储模型等

区块链可以理解为是一个去中心化不可篡改的公共数据库，在分布式网络节点之间更新和共享。

对于比特币它是一个公开账本，而对于以太坊它是一个智能合约。

使用区块链发行的加密货币，被称之为Token（通证）

从 2008 年年底中本聪发表比特币白皮书开始算起，在过去的这十多年中，比特币被死亡无数次，但已然运行了十年以上的比特币系统，可以不再被当作”实验“处理了，应该认为它很成功。虽然早期的一些其它项目很多都不再活跃甚至消失，但新的公链以及生态正在不断迭代更新。

区块链引出的一些新概念：Crypto，DAPP、NFT、DAO、DeFi、GameFi、SocialFi，Web3.0，元宇宙等等等等。

## 开发语言

- EVM: `Solidity`、`Vyper`
- Solana: `Rust`
- Libra(Aptos, Sui): `Move`
- StarkNet: `Cairo`

Vyper 是 Pythonic 的 EVM 语言，主要被 Curve 所使用。

Rust更底层更通用，并不局限于智能合约开发，但学习难度也更大。

Libra 是 Meta(Facebook) 之前计划推出的稳定币区块链项目，后因监管受阻夭折，转变成了 Diem 项目，但没过多久被迫解散，衍生出了 Aptos 和 Sui 等新的公链项目。

Move 是一种基于 Rust 的语言，最初是为 Libra 项目创造，后被 Aptos 和 Sui 等项目继续使用。

zkSync 需要先编译为中间语言Yul，然后再通过LLVM编译为 zkEVM 字节码。

StarkNet 的基础设施和智能合约均使用的是 Cairo 编程语言。

## Web3库

- JS: `Web3.js`、`Ethers.js`
- Python：`web3.py`
- Java: `Web3j`

## 框架

- JS: `Hardhat`、`Truffle`
- Python: `Brownie`、`ApeWorX`
- Rust: `Foundry`

Foundry 是 Paradigm 团队用 Rust 基于 dapptools 重构的框架，据说 `Foundry` 测试运行速度比 `Hardhat` 快 20 倍，比 `Brownie` 快 40 倍

Brownie 仍然是 Curve 协议使用的主要工具之一，但 Brownie 和 Python 社区希望 Apeworx 成为基于 Python 的智能合约框架的继任者。
