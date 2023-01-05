# web3.py

[官方文档](https://web3py.readthedocs.io/en/stable/index.html)

```bash
pip install web3

<<'COMMENT'
Successfully installed 
  aiohttp-3.8.3 aiosignal-1.3.1 async-timeout-4.0.2 attrs-22.2.0 base58-2.1.1 bitarray-2.6.1 
  certifi-2022.12.7 charset-normalizer-2.1.1 cytoolz-0.12.1 
  eth-abi-2.2.0 eth-account-0.5.9 eth-hash-0.5.1 eth-keyfile-0.5.1 eth-keys-0.3.4 eth-rlp-0.2.1 eth-typing-2.3.0 eth-utils-1.9.5 
  frozenlist-1.3.3 hexbytes-0.3.0 idna-3.4 ipfshttpclient-0.8.0a2 jsonschema-4.17.3 
  lru-dict-1.1.8 multiaddr-0.0.9 multidict-6.0.4 netaddr-0.8.0 
  parsimonious-0.8.1 protobuf-3.19.5 pycryptodome-3.16.0 pyrsistent-0.19.2 
  requests-2.28.1 rlp-2.0.1 six-1.16.0 toolz-0.12.0 
  urllib3-1.26.13 varint-1.0.2 web3-5.31.3 websockets-9.1 yarl-1.8.2
COMMENT
```

## 连接

![20220727152311](http://image.zuoright.com/20220727152311.png)

```python
from web3 import Web3

# IPCProvider，最安全
w3 = Web3(Web3.IPCProvider('./path/to/geth.ipc'))

# HTTPProvider，默认端口8545
w3 = Web3(Web3.HTTPProvider('http://127.0.0.1:8545'))

# WebsocketProvider，默认端口8546
w3 = Web3(Web3.WebsocketProvider('wss://127.0.0.1:8546'))
```

### TesterProvider

```bash
pip install "web3[tester]"

<<'COMMENT'
Successfully installed 
  cached-property-1.5.2 
  eth-bloom-1.0.4 eth-hash-0.3.3 eth-tester-0.6.0b6 
  mypy-extensions-0.4.3 py-ecc-5.2.0 py-evm-0.5.0a3 py-geth-3.8.0 pyethash-0.1.27 pysha3-1.0.2 
  semantic-version-2.10.0 sortedcontainers-2.4.0 trie-2.0.0a5 typing-extensions-3.10.0.2
COMMENT
```

```python
# TesterProvider
from web3 import Web3, EthereumTesterProvider

w3 = Web3(EthereumTesterProvider())
```

### 公共测试网

RPC Node Provider

- Infura
- Alchemy
- QuickNode

```python
import os

from dotenv import load_dotenv
from web3 import Web3

load_dotenv()

INFURA_SECRET_KEY = os.getenv("WEB3_INFURA_PROJECT_ID")

# get w3 endpoint by network name
def get_w3_by_network(network='mainnet'):
    infura_url = f'https://{network}.infura.io/v3/{INFURA_SECRET_KEY}'
    w3 = Web3(Web3.HTTPProvider(infura_url))  # 接入 Infura 节点
    return w3

w3 = get_w3_by_network(network='goerli')
```

## 基础

```python
# 验证是否连接：True / False
w3.isConnected()

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

# 查看当前区块高度
print(w3.eth.block_number)
```

- 账户

```python
# 查看账户
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

# 本地测试网可以自己添加账户
account_test = w3.eth.account.create()  # 创建账户，账户的公钥与私钥通常都用十六进制表示
account_test.key  # 查看私钥，HexBytes('0xf3273c309d2b13886ec644fb9632a25f1512a1c02cb530036b234050d013327b')
account_test.address  # 查看公钥，'0xd2CC434C59a61dA425DA241bD6a5189d11Db590c'

# 查看账户余额
balance_0 = w3.eth.get_balance(w3.eth.accounts[0])
w3.fromWei(balance_0, 'ether')  # 单位由Wei转换为Ether

# 比如查询主网V神3号钱包地址
vb_address = '0x220866b1a2219f40e72f5c628b65d54268ca3a9d'
address = Web3.toChecksumAddress(vb)  # 地址格式转换，可选
balance = w3.eth.get_balance(address) / 1e18  # 单位转换
```

## 交易

```python
from_address = Web3.toChecksumAddress("xxxx")
target_address = Web3.toChecksumAddress("yyyy")

transaction_amount = w3.toWei(10000000, 'ether')  # 交易eth数量，单位要转换为Wei
gas_limit = 21000

# 构造交易参数
params = {
    'from': from_address,  # from字段可省略，签名时会根据私钥自动推算出
    'to': target_address,
    'value': transaction_amount,
    'nonce': w3.eth.getTransactionCount(from_address),
    'gas': gas_limit,
    'gasPrice': w3.eth.gas_price,
    # 'maxFeePerGas': w3.toWei(gas_price, 'gwei'),
    # 'maxPriorityFeePerGas': w3.toWei(gas_price, 'gwei'),
    'chainId': chainId  # Goerli测试网为5
}

# 发送一笔交易
private_key_from_address = os.getenv("PRIVATE_KEY")  # 不要泄漏私钥信息
signed_tx = w3.eth.account.signTransaction(params, private_key=private_key)  # 使用发送者私钥签署交易
txn = w3.eth.sendRawTransaction(signed_tx.rawTransaction)  # 注意，这里使用的是sendRawTransaction方法

# 使用本地生成的测试地址发送交易时，系统会自动帮我们做一些工作，比如自动签名，所以可以直接发送
tx_hash = w3.eth.send_transaction(params)
```

- 查看交易明细

```python
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
