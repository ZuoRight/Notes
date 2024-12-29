# 钱包地址

## 比特币钱包地址

![20230509234516](https://image.zuoright.com/20230509234516.png)

比特币地址是交叉兼容的，每种类型的地址都可以给任意类型的地址发送资金，但是手续费不同：`原生隔离见证地址（bc1开头） < 隔离见证兼容地址（部分3开头） < 传统地址（1开头） < 多签地址（部分3开头）`

1. 隔离见证兼容地址转账手续费比传统地址节省 24%
2. 原生隔离见证地址转账手续费比传统地址节省 35%
3. 隔离见证地址转账手续费比多签地址最多可以节省 70%

### P2PKH

Pay to PubKey Hash 付款至公钥哈希，最初的传统地址（Legacy）

`Base58(0x00 + Hash160(Public Key) + Checksum)`

编码后 1 开头，34个字符，不包含 0、O、l、I 等容易混淆的字符，比如：`1MbeQFmHo9b69kCfFa6yBr7BQX4NzJFQq9`

假设Alice给Bob转账，相当于将资产放入保险箱，用Bob的公钥加密，Bob只有在向他人转账的时候，才会使用私钥签名打开保险箱，然后将其锁入另一个收款方的保险箱

```text
OP_DUP 

OP_HASH160 

(Bob 收款地址蕴含的 Public Key Hash) 

OP_EQUALVERIFY 

OP_CHECKSIG
```

### P2SH

Pay to Script Hash 付款至脚本哈希

- 多签地址：P2SH
- 隔离见证兼容地址（Nested SegWit）：P2SH-P2WPKH

`Base58(0x05 + Hash160(Script) + Checksum)`

编码后 3 开头，34个字符，比如：`3EmUH8Uh9EXE7axgyAeBsCc2vdUdKkDqWK`

### P2WPKH / Bech32

专为隔离见证设计的格式，2017 年底由 BIP173 定义

原生隔离见证地址（Native SegWit）

Base32 编码，bc1 开头，42个字符，不区分大小写，只包含数字和小写字母：0～9，a~z，比如：`bc1qj89046x7zv6pm4n00qgqp505nvljnfp6xfznyw`

QR 码更小，更容易防错，更安全，更高效，手续费更低

### P2TR / Taproot

## 以太坊钱包地址

Ethereum 有两种账号类型

- 外部账户

EOA（Externally Owned Accounts），由私钥拥有者控制，可以主动发起交易，codeHash 为空

经过 Keccak-256 哈希算法后，取最后 20 字节再加上 0x，变为 42 个字符（十六进制字符串），例如：`0x5e97870f263700f46aa00d967821199b9bc5a120`

ERC20 地址忽略大小写，TRC20 地址大小写敏感

- 合约账户

CA（Contract Accounts），由智能合约代码控制，只能被动交易，没有私钥

由 0x 开头的 42 个字符（十六进制字符串），例如：`0x06012c8cf97bead5deae237070f9587f8e7a266d`

- 抽象账户

Account Abstraction 是指将两种不同的账户合并成一种
