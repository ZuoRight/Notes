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
// 整数
int public _int = -1;  // int=int256，-2**255 ～ 2**255 - 1
type(int).min;  // 最小值
type(int).max;  // 最大值

// 正整数
uint public _unit = 1;  // unit=uint256，0 ～ 2**256 - 1
uint8 public _number = 20221209;  // 0 ～ 2**8 - 1
unit16  // 0 ~ 2**16 - 1
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

> 1Byte=8bit，每4位二进制可表示为1个十六进制数，所以一个字节可表示为2个十六进制数，即20字节用十六进制表示共40位，所以以太坊的钱包地址算上前缀0x，一共42位。

```js
// 普通地址
address public addr = 0x7A58c0Be72BE218B41C608b7Fe7C5bB630736C71;

// 可转账地址
address payable public payable_addr = payable(addr);
// 可转账地址有两个成员，方便查询余额和转账
payable_addr.balance;  // 地址余额
payable_addr.transfer(1)  // 合约向payable_addr转账1wei
```

### 字节数组

- 定长：bytes1、bytes8、bytes32
- 不定长

```js
bytes32 public bt32 = "MiniSolidity";  // 存储为32字节：0x4d696e69536f6c6964697479...
bytes1 public bt = bt32[0];  // bt32的第一个字节：0x4d
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
function <函数名> (参数类型 参数名) [函数可见性说明符] [函数权限/功能] [returns(返回值类型和名称)]{
    pass;
    return();
}
```

`this`关键字可以引用合约地址

函数可见性说明符

- private 仅合约内部可见（继承合约不可用）
- internal 仅合约内部可见（继承合约可用），默认
- external 仅合约外部可见（但内部可以用`this.f()`调用）
- public 合约内部外部均可见，给变量加public实际就是创建了getter函数

函数权限/功能

- 默认为空既能读取也能写入状态变量
- view 能读取但是不能写入状态变量，不消耗Gas
- pure 不能读取也不能写入状态变量，不消耗Gas
- payable 可支付的

解构式赋值

```js
uint256 _number;
bool _bool;
uint256[3] memory _array;
// 假设returnNamed()函数有三个返回值
(_number, _bool, _array) = returnNamed();  // 读取所有变量
(, _bool2, ) = returnNamed();  // 读取部分变量
```

### 变量作用域

- 状态变量（state variable）

在合约内、函数外声明，状态变量数据存储在链上，即storage，所有合约内函数都可以访问

- 局部变量（local variable）

在函数内声明，仅在函数执行过程中有效，存储在内存里，不上链

- 全局变量（global variable）

无需声明，是solidity预留关键字，可直接使用

```js
// 常用全局变量
blockhash(uint blockNumber): (bytes32)给定区块的哈希值 – 只适用于256最近区块, 不包含当前区块。
gasleft(): (uint256) 剩余 gas

block.coinbase: (address payable) 当前区块矿工的地址
block.gaslimit: (uint) 当前区块的gaslimit
block.number: (uint) 当前区块的number
block.timestamp: (uint) 当前区块的时间戳，为unix纪元以来的秒

msg.data: (bytes calldata) 完整call data
msg.sender: (address payable) 消息发送者 (当前 caller)
msg.sig: (bytes4) calldata的前四个字节 (function identifier)
msg.value: (uint) 当前交易发送的wei值
```

## 引用类型

### 数组

```js
// 固定长度，T[k]
uint[8] array1;
address[100] array3;
bytes1[5] array2;

// 可变长度，T[]
uint[] array4;
address[] array6;
bytes1[] array5;
bytes array7;  // 不用加[]，Gas比bytes1[]要便宜
```

方法

```js
.length  // 数组包含的元素数量
push(x)  // 向数组末尾插入x元素，省略x则插入0
pop()  // 移除数组末尾元素
```

### 结构体

```js
// 定义结构体
struct Student{
    uint256 id;
    uint256 score; 
}
// 使用自定义的结构体声明一个变量
Student storage st;
// 赋值
st.id = 11;
st.score = 100;
```

### 映射

```js
// 格式：mapping(KeyType => ValueType)
// 映射的KeyType不能使用自定义的结构体
// 映射的存储位置必须是storage
mapping(uint => address) public idToAddress;  // id映射到地址
mapping(address => address) public swapPair;  // 币对的映射，地址到地址

idToAddress[_Key] = _Value;  // 新增
```
