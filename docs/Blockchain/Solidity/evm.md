# EVM

以太坊虚拟机(EVM)是智能合约的运行环境，合约之间是完全隔离的

## 两种账户

- 外部账户：由公钥-私钥对控制
- 合约账户：由合约代码控制

## 数据存储位置 data location

每个账户都有一个持久的键值存储，将256位的字映射到256位的字，称为存储

主要针对于这几种类型：数组（包含string和bytes）、结构体、映射

- `stack` 堆栈
- `memory` 函数里的参数和临时变量等一般存在这里，类似计算机内存
- `calldata` 和memory类似，但不能修改
- `storage` 存储在链上，类似计算机硬盘，消耗Gas最多，合约里的状态变量默认都存在这里
- `code`
- `logs`

uint256 仅仅存储于内存中

函数参数只能用 memory 或 calldata