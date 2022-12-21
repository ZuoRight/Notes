# Solidity

[官方文档](https://docs.soliditylang.org/en/latest/index.html){ .md-button .md-button--primary }

Solidity 是一种面向对象的高级语言，用于实现智能合约。

> 智能合约是管理以太坊状态下账户行为的程序。

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

## 数据类型

### 布尔型

- true
- false

```js
bool public _bool = true;
```

布尔运算符

```js
&& 与
|| 或
! 非
```

### 整型

```js
int public _int = -1;  // 正整数、负整数
uint public _unit = 1;  // 正整数
uint8 public _number = 20221209;  // 256位正整数
```

整型运算符

```js
// 比较运算符（返回布尔值）
== !=
> >=
< <=

// 算数运算符
+ - * /
%  取余
** 乘方（幂）
```

### 地址类型

地址类型存储一个20字节的值

```js
// 普通地址
address public _address = 0x7A58c0Be72BE218B41C608b7Fe7C5bB630736C71;

// 可转账地址
address payable public payable_address = payable(_address);
// 可转账地址有两个成员，方便查询余额和转账
payable_address.balance;  // 地址余额
payable_address.transfer(1)  // 合约向payable_address转账1wei
```

### 字节数组

- 定长：bytes1、bytes8、bytes32
- 不定长

1Byte=8bit，每4位二进制可表示为1个十六进制数，所以一个字节可表示为2个十六进制数，即32字节用十六进制表示共64位

```js
bytes32 public _byte32 = "MiniSolidity";  // 存储为32字节：0x4d696e69536f6c6964697479...
bytes1 public _byte = _byte32[0];  // _byte32的第一个字节：0x4d
```

### 枚举

主要用于为uint分配名称，使程序易于阅读和维护

```js
enum ActionSet {Buy, Hold, Sell};  // 自定义枚举类型ActionSet
ActionSet action = ActionSet.Buy  // 创建ActionSet类型变量并赋值

// 枚举类型可以显式的和uint相互转换
uint(action);  // 0
```

## 函数

```js
function <function name> (<parameter types>) {internal|external|public|private} [pure|view|payable] [returns (<return types>)]
```
