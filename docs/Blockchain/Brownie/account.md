# 账户管理

## 测试账户

使用本地环境默认生成 10 个测试账户

```python
from brownie import accounts
# 直接使用即可
account = accounts[0]
```

## 本地账户

通过托管节点连接远程网络时，因为是公网，所以并不会像本地开发一样自动创建测试账户，需要自己添加账户，Local accounts are stored in encrypted JSON files known as [keystores](https://julien-maffre.medium.com/what-is-an-ethereum-keystore-file-86c8c5917b97).

- 添加 Local accounts

```shell
# 生成一个新账户
brownie accounts generate <id>  # 回车后设置密码，然后生成随机私钥

# 从私钥导入
# Private Key 可以是自己用 MetaMask 等钱包工具创建的
brownie accounts new <id>  # 回车后输入私钥和密码

<<'COMMENT'
Enter the private key you wish to add:
xxx04ea3c...6f71b4b9d1...xxx...81c2423c608...336068xxx
Enter the password to encrypt this account with: 
SUCCESS: A new account '0x28Fab2EbF6eA4D749F0aC71556F957CE56Ce05Cf' has been generated with the id 'demo'
COMMENT

# 从Keystore导入
brownie accounts import <id> <path>
# 导出到Keystore
brownie accounts export <id> <path>
```

- 删除 Local accounts

```shell
brownie accounts delete <id>
```

- 查看 Local accounts

```shell
brownie accounts list

<<'COMMENT'
Found 1 account:
 └─demo: 0x28Fab2EbF6eA4D749F...71556F957CE56Ce05Cf
COMMENT
```

- 使用 Local accounts

Local accounts 账户不能直接使用，必须先 unlock

```python
# 私钥存储在Keystore中
from brownie import accounts
account = accounts.load("demo")
```

```python
# 私钥存储在环境变量中，直接获取
"""
export PRIVATE_KEY=0xabc...
"""
from brownie import accounts
account = accounts.add(os.getenv("PRIVATE_KEY"))
```

```python
# 私钥存储在环境变量中，从brownie-config.yaml中间接获取
"""
wallets: 
  from_key: ${PRIVATE_KEY}
"""
from brownie import accounts, config
account = accounts.add(config["wallets"]["from_key"])
```

## 签名消息

- Signing Raw Text Messages (EIP-191)

```python
local = accounts.load("demo")

msg = "hello world"
signed = local.sign_defunct_message(msg)
signed.messageHash.hex()  # '0xd9eba16ed0ecae432b71fe008c98cc872bb4cc214d3220a36f365326cf807d68'
```

- Signing Messages (EIP-712)

```python
from eip712.messages import EIP712Message, EIP712Type

local = accounts.load("demo")

class TestSubType(EIP712Type):
    inner: "uint256"

class TestMessage(EIP712Message):
    _name_: "string" = "hello world"
    outer: "uint256"
    sub: TestSubType

msg = TestMessage(outer=1, sub=TestSubType(inner=2))
signed = local.sign_message(msg)
signed.messageHash.hex()  # '0x3ca0ec0c3a8efcdfbd72f5fa7ef2435d28c8fa73e1d04416cba24c78c9da5e04'
```
