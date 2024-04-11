# 引言

[官方文档](https://docs.soliditylang.org/en/latest/index.html){ .md-button .md-button--primary }

Solidity 是一种面向对象的高级语言，用于实现智能合约。

> 智能合约是管理以太坊状态下账户行为的程序

## 开发环境

以太坊官方推荐的Remix IDE，集成了Solidity编译器和运行时环境

Docs: <https://remix-ide.readthedocs.io/en/latest/index.html>

在线IDE：<https://remix.ethereum.org>

```text
蓝色按钮表示只读方法，不需要支付Gas，可变性为view或pure的函数，或者状态变量
橙色按钮表示写入方法，需要支付Gas
红色按钮表示可以向合约支付，可变性为payable的函数
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

## 常量

常量在被合约调用时所消耗的Gas要比变量少

```js
uint public constant MY_NUMBER = 777;
address public constant MY_ADDRESS = 0xabc123...;
```

## 变量

### 状态变量（state variable）

在合约内、函数外声明，状态变量数据默认存储在链上，即 `storage`，所有合约内函数都可以访问

### 局部变量（local variable）

在函数内声明，仅在函数执行过程中有效，存储在虚拟机内存里，即 `memory`，不上链

### 全局变量（global variable）

预留关键字，无需声明，可直接使用

```js
block.number: (uint)  // 当前区块高度
block.timestamp: (uint)  // 当前区块的时间戳
block.coinbase: (address payable)  // 当前区块矿工的地址
block.gaslimit: (uint)  // 当前区块的gaslimit

msg.sender: (address payable)  // 调用者地址 caller
msg.sender.balance  // 进行交易的用户的余额，如果msg.sender是另一个合约，那么它是另一个合约的余额
msg.sig: (bytes4)  // calldata的前四个字节 (function identifier)
msg.value: (uint)  // 当前交易发送的wei值
msg.data: (bytes calldata)  // 完整call data
```

## 变量默认初始值

在solidity中，声明但没赋值的变量都有它的默认初始值

`delete x` 方法可以让变量恢复为初始值

```js
bool public _bool; // false
string public _string; // ""
uint public _uint; // 0
int public _int; // 0
address public _address; // 20Bytes，十六进制40个0，0x0000000000000000000000000000000000000000
bytes32 public b32;  // 32Bytes，十六进制64个0，0x000...000

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
