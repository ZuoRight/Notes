# 普通账户模型

账户模型，即记账模式，以太坊采用常见的普通账户模型，也叫账户余额模型（Account-Balance），相对更自由

参考：<https://time.geekbang.org/column/article/6511>

账户拥有4个参数：`{nonce, balance, codeHash, StorageRoot}`

合约账户调用其它合约有两种方法

```js
call()  // 普通的消息调用，是跳转到被调用合约执行完相应代码返回后继续执行后续代码
delegatecall()  // 委托调用是特殊的消息调用，把被调用合约中的代码拷贝过来执行
```

## Approve

对于 ERC20 代币来说，会有两个账本，一个维护用户余额（balance），另一个维护用户授权列表和授权额度（allowance）

![20230603160539](http://image.zuoright.com/20230603160539.png)

![20230603160752](http://image.zuoright.com/20230603160752.png)

### Permit2

Permit2 合约支持使用签名而不是交易来完成代币的转账授权，并且支持传统的 ERC20 合约

<https://learnblockchain.cn/article/5723>
