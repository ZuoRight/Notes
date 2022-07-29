# 以太坊

[以太坊历史](https://ethereum.org/en/history/)

[官方文档](https://ethereum.org/en/developers/docs/intro-to-ethereum/)

以太坊由俄裔加拿大程序员Vitalik Buterin（维塔利克·布特林，俗称V神，94年）与 Gavin Wood（英国人，计算机科学家，80年） 等人联合发起

V神写了以太坊的[白皮书](https://ethereum.org/zh/whitepaper/)

Gavin Wood 写了以太坊的[黄皮书](https://github.com/ethereum/yellowpaper)（[中文版](https://github.com/yuange1024/ethereum_yellowpaper/blob/master/ethereum_yellow_paper_cn.pdf)），是以太坊基金的第一位CTO，提出并参与开发了EVM的编程语言Solidity，2016年离开以太坊基金，后来还开发了Rust编写了以太坊的客户端Parity，后又联合创办了另一个底层公链Polkadot，并为其写了白皮书。

以太坊将交易的编程语言改为了图灵完备，称之为智能合约，引发了公链生态，虽然他自己一开始也并没有想到

![20220729195329](http://image.zuoright.com/20220729195329.png)

## 单位

计算机处理浮点数通常会有误差，所以一般涉及到金钱相关的会缩小单位，比如1元，通常存储为100分

以太坊中规定：`1 eth` = `10^18 wei` = `10^9 gwei`

- wei 是以太坊中的最小单位（以b-money的发明者 Wei Dai 命名），常用于开发中
- gwei 主要用于表示Gas费

## Gas

Gas是以太坊交易中的手续费，可以防止恶意交易以助于保持网络的安全，价格以 gwei 表示

一笔交易消耗的Gas上限为21000个，多退，但如果少了则交易失败且不会退还。

每个Gas的价格是动态变化的，总的Gas费用计算方式

- 伦敦升级前：`Gas units (limit) * Gas price per unit`
- 伦敦升级后：`Gas units (limit) * (Base fee + Tip)`

其中Base fee会被销毁，交易越多下一个区块的Base fee越高（最低100gwei）

Tip（小费，也叫优先费）奖励给矿工，所以矿工正常会优先打包Tip较大的交易

发送方可以设置maxFeePerGas，会根据真实消耗费用把差额退还给发送方：`refund = max fee - (base fee + priority fee)`

## 挖矿

以太坊发行总量没有上限，而且交易过程中会不断地销毁

- 关于区块的大小限制

比特币一个区块大小是1MB，里面可以包含1500到2000笔交易。而以太坊区块会根据运行在上面的智能合约的复杂程度决定，区块目标大小为1500万个gas，最高3000万个gas，所以一个区块的总交易数量为：`gas总数/21000`，理论上最高不过1428笔，如果当前区块超过1500万个gas，则下一个区块会提高Base fee，以此降低交易数量（因为手续费增加，理论上交易的人就会相对变少）。

- 关于区块的产出速率

比特币主要用于支付，需要更多的安全确认，所以限制平均10min出一个块，而以太坊则是执行智能合约为主，支付功能并不是以太坊的主要功能，平均13s左右。

## 与链交互

以太坊是一个由分布式节点构成的去中心化计算机网络

[节点实时分布图](https://etherscan.io/nodetracker)

节点分类

- Full node(全节点)：存储完整的区块链数据，通常用于挖矿
- Light node(轻节点)：只存储区块头，目前以太坊还不支持大量的轻节点，对应的客户端也几乎没有
- Archive node(存档节点)：通常用于区块链浏览器和钱包服务等
- Simulated node(模拟节点)：用于本地开发测试

客户端是节点的一种实现，以太坊升级前(Eth1.0)只需要运行一个执行客户端(execution clients)，其中Go语言编写的Geth是最早也是使用最多的客户端

![20220727154624](http://image.zuoright.com/20220727154624.png)

升级后(Eth2.0)需要再运行一个共识客户端(consensus clients)

![20220727155848](http://image.zuoright.com/20220727155848.png)

升级后交互示意图

![20220727154452](http://image.zuoright.com/20220727154452.png)

web3库有各种语言的实现

- JS: `Web3.js`、`Ethers.js`
- Java: `Web3j`
- Python：`web3.py`

[官方文档](https://web3py.readthedocs.io/en/stable/index.html)

```bash
pip install web3
"""
Successfully installed aiohttp-3.8.1 aiosignal-1.2.0 async-timeout-4.0.2 attrs-21.4.0 base58-2.1.1 bitarray-2.6.0 certifi-2022.6.15 charset-normalizer-2.1.0 cytoolz-0.12.0 eth-abi-2.2.0 eth-account-0.5.8 eth-hash-0.5.0 eth-keyfile-0.5.1 eth-keys-0.3.4 eth-rlp-0.2.1 eth-typing-2.3.0 eth-utils-1.9.5 frozenlist-1.3.0 hexbytes-0.2.2 idna-3.3 ipfshttpclient-0.8.0a2 jsonschema-4.7.2 lru-dict-1.1.8 multiaddr-0.0.9 multidict-6.0.2 netaddr-0.8.0 parsimonious-0.8.1 protobuf-3.20.1 pycryptodome-3.15.0 pyrsistent-0.18.1 requests-2.28.1 rlp-2.0.1 toolz-0.12.0 urllib3-1.26.11 varint-1.0.2 web3-5.30.0 websockets-9.1 yarl-1.7.2
"""
```

连接方式

![20220727152311](http://image.zuoright.com/20220727152311.png)

- 本地节点

```python
from web3 import Web3

# IPCProvider，最安全
w3 = Web3(Web3.IPCProvider('./path/to/geth.ipc'))

# HTTPProvider，默认端口8545
w3 = Web3(Web3.HTTPProvider('http://127.0.0.1:8545'))

# WebsocketProvider，默认端口8546
w3 = Web3(Web3.WebsocketProvider('wss://127.0.0.1:8546'))
```

- 模拟节点

使用真实节点同步区块链数据到本地非常耗时，本地开发可使用模拟的测试节点

```bash
pip install "web3[tester]"
"""
Successfully installed cached-property-1.5.2 eth-bloom-1.0.4 eth-hash-0.3.3 eth-tester-0.6.0b6 mypy-extensions-0.4.3 py-ecc-5.2.0 py-evm-0.5.0a3 py-geth-3.8.0 pyethash-0.1.27 pysha3-1.0.2 semantic-version-2.10.0 sortedcontainers-2.4.0 trie-2.0.0a5 typing-extensions-3.10.0.2
"""
```

```python
from web3 import Web3, EthereumTesterProvider

# TesterProvider
w3 = Web3(EthereumTesterProvider())

# 验证是否连接
w3.isConnected()  # True/False

# 查看TesterProvider提供的账户
w3.eth.accounts
"""
[
 '0x7E5F4552091A69125d5DfCb7b8C2659029395Bdf',
 '0x2B5AD5c4795c026514f8317c7a215E218DcCD6cF',
 '0x6813Eb9362372EEF6200f3b1dbC3f819671cBA69',
 '0x1efF47bc3a10a45D4B230B5d10E37751FE6AA718',
 '0xe1AB8145F7E55DC933d51a18c793F901A3A0b276',
 '0xE57bFE9F44b819898F47BF37E5AF72a0783e1141',
 '0xd41c057fd1c78805AAC12B0A94a405c0461A6FBb',
 '0xF1F6619B38A98d6De0800F1DefC0a6399eB6d30C',
 '0xF7Edc8FA1eCc32967F827C9043FcAe6ba73afA5c',
 '0x4CCeBa2d7D2B4fdcE4304d3e09a1fea9fbEb1528'
]
"""

# 查看账户余额
balance_0 = w3.eth.get_balance(w3.eth.accounts[0])
w3.fromWei(balance_0, 'ether')  # Decimal('1000000') 100万eth测试币

# 查看最新区块信息
w3.eth.get_block('latest')  # 首次查询是一个创世区块：parentHash为空字节，transactions为空列表
"""
AttributeDict({'number': 0,
 'hash': HexBytes('0xe491538d619b14f69bd88c4214dbea0581e8c1a4e688390797235e43bc57f7aa'),
 'parentHash': HexBytes('0x0000000000000000000000000000000000000000000000000000000000000000'),
 'nonce': HexBytes('0x000000000000002a'),
 'sha3Uncles': HexBytes('0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347'),
 'logs_bloom': 0,
 'transactionsRoot': HexBytes('0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421'),
 'receipts_root': '0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421',
 'stateRoot': HexBytes('0xf1588db9a9f1ed91effabdec31f93cb4212b008c8b8ba047fd55fabebf6fd727'),
 'miner': '0x0000000000000000000000000000000000000000',
 'difficulty': 131072,
 'totalDifficulty': 131072,
 'size': 517,
 'extraData': HexBytes('0x0000000000000000000000000000000000000000000000000000000000000000'),
 'gasLimit': 30029122,
 'gasUsed': 0,
 'timestamp': 1658900721,
 'transactions': [],
 'uncles': [],
 'baseFeePerGas': 1000000000})
"""

# 发送一笔交易
tx_hash = w3.eth.send_transaction({
   'from': w3.eth.accounts[0],  # 发送方
   'to': w3.eth.accounts[1],  # 接收方
   'value': w3.toWei(3, 'ether')  # 交易数量，要转换为wei
})

# 查看交易
w3.eth.get_transaction(tx_hash)
"""
AttributeDict({'type': '0x2',
 'hash': HexBytes('0x9e473ea541a2df4b180a45585a7751e48a753f7ba6e5d46f35d59bff6046b4bb'),
 'nonce': 0,
 'blockHash': HexBytes('0xedd65d581383e8bd0066ba5b464c1d9cb9550be3673372cf0bca9f03c0fa2c31'),
 'blockNumber': 1,
 'transactionIndex': 0,
 'from': '0x7E5F4552091A69125d5DfCb7b8C2659029395Bdf',
 'to': '0x2B5AD5c4795c026514f8317c7a215E218DcCD6cF',
 'value': 3000000000000000000,
 'gas': 121000,
 'data': '0x',
 'r': HexBytes('0x6ee7b2551baa2de937502e2286a6dca35164b5e9a4a7d4a6fff4c616e5f3e671'),
 's': HexBytes('0x441615d3bde4467c387fd45455909fa243c4881d17c9fa83ccd2dc0e7eb5bb1d'),
 'v': 0,
 'chain_id': 131277322940537,
 'maxFeePerGas': 1000000000,
 'maxPriorityFeePerGas': 1000000000,
 'accessList': [],
 'gasPrice': 1000000000})
"""

# 分别检查请求方和接收方余额
"""
发现接收方确实增加了3个eth
但发送方却少了不止3个eth，这是因为还消耗了一些手续费(gas)
"""
w3.eth.get_balance(w3.eth.accounts[0])  # 999996999979000000000000
w3.eth.get_balance(w3.eth.accounts[1])  # 1000003000000000000000000
```

## 账户相关

```python
from web3 import Web3

w3 = Web3(Web3.EthereumTesterProvider())  # 不连接节点也可以创建，不过区块链中无法查到
account_test = w3.eth.account.create()  # 创建账户，账户的公钥与私钥通常都用十六进制表示
account_test.key  # 查看私钥，HexBytes('0xf3273c309d2b13886ec644fb9632a25f1512a1c02cb530036b234050d013327b')
account_test.address  # 查看公钥，'0xd2CC434C59a61dA425DA241bD6a5189d11Db590c'

# 从account_one发送一些测试币到account_test
account_one = w3.eth.accounts[0]
tx_hash = w3.eth.send_transaction({
    'from': account_one,
    'to': account_test.address,
    'value': Web3.toWei(1, 'ether')
})

# 从account_test发送测试币到account_one
"""
tx_hash = w3.eth.send_transaction({
    'from': account_test.address,
    'to': account_one,
    'value': Web3.toWei(0.5, 'ether')
})

如果这样直接发送的话会报ValidationError: No valid "from" key was provided in the transaction which is required for transaction signing.
这是因为使用系统提供的account_one账户发送时，系统为了方便自动帮我们做了一些工作，而我们用自己创建的account_test账户发送时则需要手动签名并广播到网络
"""
# 1. 手动构建交易细节
tx = {
    # 不需要from字段，签名时会根据私钥自动推算出发送者地址
    'to': account_one,
    'value': 10000000,
    'gas': 21000,
    'gasPrice': w3.eth.get_block('pending')['baseFeePerGas'],
    'nonce': 0  # 交易计数，0表示第1笔交易
}
# 2. 使用发送者私钥签署交易
signed = w3.eth.account.sign_transaction(tx, account_test.key)

# 3. 将原始交易广播到网络，注意，这里使用的是send_raw_transaction方法
tx_hash = w3.eth.send_raw_transaction(signed.rawTransaction)
```
