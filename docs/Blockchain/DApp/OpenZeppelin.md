# OpenZeppelin

OppenZepplin 是一个维护 Solidity 标准化代码库的组织

- Docs: <https://docs.openzeppelin.com/>
- Contracts Wizard: <https://docs.openzeppelin.com/contracts/4.x/wizard>
- Github: <https://github.com/OpenZeppelin>

OpenZeppelin 是构建在 EVM 之上的开源智能合约开发工具，开发者可以安全的开发和管理智能合约和 Dapp，使用 Solidity 进行构建，支持所有 EVM 和 eWASM，在以太坊开发者社群中极为出名，是最流行的 Solidity 开源合约标准库，其中有上百个合约，Github 上获得 21,000+ star。因其经过审核的智能合约和库代表着产业标准。

举例而言，几乎所有关于开发者如何部署 ERC-20 代币的教程都是使用 OpenZeppelin 合约作为范例。

OpenZeppelin 的另一项主要业务是代码审计，参与过行业内包括 Cosmos 和诸多 DeFi 在内的大量明星项目的代码审计。

```shell
npm install @openzeppelin/contracts
# 安装到当前路径下的node_modules/中
```

## 在 Solidity 中使用

- Ownable

```js
import "@openzeppelin/contracts/access/Ownable.sol";

contract Demo is Ownable {
    function foo() onlyOwner {
        pass;
    }
}
```

## 在 Brownie 中使用

Brownie 允许您将其他项目作为包安装

> Docs: <https://eth-brownie.readthedocs.io/en/stable/package-manager.html>

```shell
# 从Github安装
brownie pm install OpenZeppelin/openzeppelin-contracts@3.0.0

# 从ethPM(以太坊包管理器)安装
$ brownie pm install ethpm://zeppelin.snakecharmers.eth:1/math@1.0.0

# 查看已安装包
brownie pm list

`
Brownie v1.19.2 - Python development framework for Ethereum

The following packages are currently installed:

smartcontractkit
 └─smartcontractkit/chainlink-brownie-contracts@0.5.1

OpenZeppelin
 └─OpenZeppelin/openzeppelin-contracts@4.8.0
`
```

## 可升级合约

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "@openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol";
import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

contract MyToken is Initializable, ERC20Upgradeable {
    /// @custom:oz-upgrades-unsafe-allow constructor
    constructor() {
        _disableInitializers();
    }

    function initialize() initializer public {
        __ERC20_init("MyToken", "MTK");
    }
}
```
