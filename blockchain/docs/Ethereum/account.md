# 账户模型

以太坊采用常见的普通账户体系

## 账户类型

账户拥有4个参数：`{nonce, balance, codeHash, StorageRoot}`

账户地址都是20字节

### 外部账户

EOA（Externally Owned Accounts），由私钥拥有者控制，可以主动发起交易，codeHash 为空

### 合约账户

CA（Contract Accounts），由智能合约代码控制，只能被动交易，没有私钥

调用合约有两种方法

```js
call()  // 普通的消息调用，是跳转到被调用合约执行完相应代码返回后继续执行后续代码
delegatecall()  // 委托调用是特殊的消息调用，把被调用合约中的代码拷贝过来执行
```
