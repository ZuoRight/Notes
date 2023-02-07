# 引言

[官方文档](https://docs.soliditylang.org/en/latest/index.html){ .md-button .md-button--primary }

Solidity 是一种面向对象的高级语言，用于实现智能合约。

> 智能合约是管理以太坊状态下账户行为的程序

## 开发环境

以太坊官方推荐的Remix IDE，集成了Solidity编译器和运行时环境

Docs: <https://remix-ide.readthedocs.io/en/latest/index.html>

在线IDE：<https://remix.ethereum.org>

```Plain text
红色按钮表示可以向合约支付，即payable
橙色按钮表示需要支付Gas
蓝色按钮表示不需要支付Gas
```

本地开发可使用：`VSCode + Solidity插件 + Brownie等框架`

## 规范

代码第一行需要注释智能合约所用的许可证，如果不写编译时会警告

> SPDX-License-Identifier，即遵循SPDX(Software Package Data Exchange)国际标准规范的许可证

语句以分号结尾

```js
// SPDX-License-Identifier: MIT
pragma solidity >=0.8.4 <0.9.0;  // 声明版本，可简写为：^0.8.4，^表示向上兼容

// 创建合约
contract HelloWeb3{
    string public _string = "Hello Web3!";
}
```
