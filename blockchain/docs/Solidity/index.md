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

```js
// SPDX-License-Identifier: MIT
pragma solidity >=0.8.4 <0.9.0;
/**
 * 上面第一行代码：需要注释智能合约所用的许可证，如果不写编译时会警告
 *     遵循SPDX(Software Package Data Exchange)国际标准规范的许可证
 *     如果没想好或者没有则可以写成：UNLICENSED
 * 上面第二行代码：Pragmas 是编译器关于如何处理源代码的常用指令，比如用来声明编译器版本
 *     可简写为：^0.8.4，^表示向上兼容
 */

// 语句以分号结尾
contract Coin {
    // 声明一个address类型的状态变量
    address public minter;  // 关键字public使变量可以被其他合约访问
    mapping (address => uint) public balances;

    // 凡是引起状态改变的操作，都应该记一个事件，类似日志，命名通常首字母大写
    event Sent(address from, address to, uint amount);

    // 构造函数代码只有在合约创建时运行
    constructor() {
        minter = msg.sender;
    }

    // 向一个地址发送一定数量的新创建的代币
    // 但只能由合约创建者调用
    function mint(address receiver, uint amount) public {
        require(msg.sender == minter);
        balances[receiver] += amount;
    }

    // 错误类型变量允许您提供关于操作失败原因的信息，它们会返回给函数的调用者
    error InsufficientBalance(uint requested, uint available);

    // 从任何调用者那里发送一定数量的代币到一个地址
    function send(address receiver, uint amount) public {
        if (amount > balances[msg.sender])
            revert InsufficientBalance({
                requested: amount,
                available: balances[msg.sender]
            });

        balances[msg.sender] -= amount;
        balances[receiver] += amount;
        emit Sent(msg.sender, receiver, amount);
    }
}
```

## 初始值

在solidity中，声明但没赋值的变量都有它的初始值或默认值

`delete x` 方法可以让变量恢复为初始值

```js
bool public _bool; // false
string public _string; // ""
int public _int; // 0
uint public _uint; // 0
address public _address; // 0x0000000000000000000000000000000000000000

enum ActionSet { Buy, Hold, Sell}
ActionSet public _enum; // 第一个元素 0

function fi() internal{} // internal空白方程
function fe() external{} // external空白方程

// Reference Types
uint[8] public _staticArray; // 所有成员设为其默认值的静态数组[0,0,0,0,0,0,0,0]
uint[] public _dynamicArray; // `[]`
mapping(uint => address) public _mapping; // 所有元素都为其默认值的mapping
// 所有成员设为其默认值的结构体 0, 0
struct Student{
    uint256 id;
    uint256 score; 
}
Student public student;
```
