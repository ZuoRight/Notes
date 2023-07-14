# DApp

- Web1.0 所见即所得
- Web2.0 所荐即所得
- Web3.0 所建即所得

## 架构

> [参考文章](https://www.preethikasireddy.com/post/the-architecture-of-a-web-3-0-application)

![20230610215948](http://image.zuoright.com/20230610215948.png)

![20230610215825](http://image.zuoright.com/20230610215825.png)

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

![20230218003925](http://image.zuoright.com/20230218003925.png)

## Web3库

- JS: `web3.js`、`ethers.js`
- Python：`web3.py`
- Java: `Web3j`

## 框架

- JS: `Truffle`、`Hardhat`
- Python: `Brownie`、`ApeWorX`
- Solidity: [`Foundry`](https://getfoundry.sh/)

Truffle 基于 web3.js，Hardhat 基于 ethers.js

Brownie 是 Curve 协议使用的主要工具之一，但 Brownie 和 Python 社区希望 Apeworx 成为基于 Python 的智能合约框架的继任者。

Foundry 是 Paradigm 团队用 Rust 基于 dapptools 重构的框架，据说 `Foundry` 测试运行速度比 `Hardhat` 快 20 倍，比 `Brownie` 快 40 倍
