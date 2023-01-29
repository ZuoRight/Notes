# Brownie

Brownie is Python development framework for Ethereum，support for Solidity and Vyper，relies heavily upon Web3.py.

Docs: <https://eth-brownie.readthedocs.io/en/stable/index.html>

## 安装

```bash
# 框架依赖较多，建议安装到虚拟环境中
pip install eth-brownie

<<'COMMENT'
Successfully installed
    aiosignal-1.2.0 asttokens-2.0.5 attrs-22.1.0 bitarray-2.6.0 black-22.10.0 
    certifi-2022.9.24 click-8.1.3 cytoolz-0.12.0 dataclassy-0.11.1 
    eip712-0.1.0 eth-brownie-1.19.2 eth-event-1.2.3 eth-hash-0.3.3 eth-utils-1.10.0 execnet-1.9.0 
    frozenlist-1.3.1 hexbytes-0.2.3 hypothesis-6.27.3 inflection-0.5.0 iniconfig-1.1.1 jsonschema-3.2.0 
    lazy-object-proxy-1.7.1 multidict-6.0.2 mypy-extensions-0.4.3 mythx-models-1.9.1 
    packaging-21.3 pathspec-0.10.1 platformdirs-2.5.2 pluggy-1.0.0 prompt-toolkit-3.0.31 psutil-5.9.2 py-1.11.0 
    py-solc-ast-1.2.9 pycryptodome-3.15.0 pygments-2.13.0 pygments-lexer-solidity-0.7.0 pyjwt-1.7.1 pyparsing-3.0.9 
    pyrsistent-0.18.1 pytest-6.2.5 pytest-forked-1.4.0 pytest-xdist-1.34.0 python-dateutil-2.8.1 python-dotenv-0.16.0 pythx-1.6.1 pyyaml-5.4.1 
    sortedcontainers-2.4.0 toml-0.10.2 tomli-2.0.1 tqdm-4.64.1 typing-extensions-4.4.0 
    urllib3-1.26.12 vvm-0.1.0 vyper-0.3.7 wcwidth-0.2.5 web3-5.31.1 wheel-0.37.1 wrapt-1.14.1 yarl-1.8.1
COMMENT

brownie --version  # Brownie v1.19.2 - Python development framework for Ethereum
```

## 项目初始化

```bash
mkdir project_name
cd project_name

brownie init

<<'COMMENT'
Brownie v1.19.2 - Python development framework for Ethereum

SUCCESS: A new Brownie project has been initialized at /Users/chonge/code/blockchain/brownie_simple_storage
COMMENT
```

另外 brownie 预制了一些项目模版，可以直接使用模版快速初始化项目，比如react、github-actions等

比如基于ERC-20创建Token：`brownie bake token`，这将会自动从<https://github.com/brownie-mix>下载对应的模版到本地

## 项目结构

```plain text
contracts/: Contract sources
interfaces/: Interface sources
scripts/: Scripts for deployment and interaction
tests/: Scripts for testing the project
build/: Project data such as compiler artifacts and unit test results
reports/: JSON report files for use in the GUI
```

## 使用流程

1. 初始化项目
2. 将合约源码放入`contracts/...`路径下
3. 编译合约源码（可以不用手动编译，每次加载时Brownie会自动编译）
4. 在`scripts/...`路径下编写Python脚本用于部署和与智能合约交互
5. 部署脚本到本地测试区块链网络，借助Ganache
6. 在`tests/...`路径下编写单元测试脚本，验证合约功能
7. 部署脚本到公共测试区块链网络，比如Goerli

也可以通过Brownie自带的控制台快速测试本地合约，或者与链上合约交互

```bash
# brownie shell is a python shell with all of our smart contract features
brownie console

<<'COMMENT'
Brownie v1.19.2 - Python development framework for Ethereum

SimpleStorageBrownieProject is the active project.

Launching 'ganache-cli --chain.vmErrorsOnRPCResponse true --wallet.totalAccounts 10 --hardfork istanbul --miner.blockGasLimit 12000000 --wallet.mnemonic brownie --server.port 8545'...
Brownie environment is ready.
>>>

>>> dir(accounts)  # 查看类的方法和属性
[add, at, clear, connect_to_clef, default, disconnect_from_clef, from_mnemonic, load, remove]

>>> help(accounts.add)  # 查看帮助

>>> run('脚本名')  # 运行scripts路径的脚本

>>> quit()  # Use quit() or Ctrl-D (i.e. EOF) to exit
COMMENT
```

## 编译

```bash
brownie compile

<<'COMMENT'
Brownie v1.19.2 - Python development framework for Ethereum

New compatible solc version available: 0.8.7
Compiling contracts...
  Solc version: 0.8.7
  Optimizer: Enabled  Runs: 200
  EVM Version: Istanbul
Generating build data...
 - SimpleStorage

Project has been compiled. Build artifacts saved at /Users/chonge/code/blockchain/brownie_simple_storage/build/contracts
COMMENT
```

可以通过配置文件优化编译设置，比如指定Solidity版本等，[参考文档](https://eth-brownie.readthedocs.io/en/stable/compile.html#compile-settings)

```plain text
compiler:
    evm_version: null
    solc:
        version: null
        optimizer:
            enabled: true
            runs: 200
    vyper:
        version: null
```

## 部署

```python
from brownie import *

# 入口函数
def main():
    print("Hello!")
    # Token.deploy("Test Token", "TEST", 18, 1e23, {'from': accounts[0]})
```

- 运行

```bash
brownie run scripts/deploy.py [--network goerli]

# 默认使用ganache-cli创建并运行本地区块链网络，也可以替换成hardhat、anvil等
# 也可以 --network 使用infura测试网，需要在.env文件中export WEB3_INFURA_PROJECT_ID（固定写法）

<<'COMMENT'
BrownieSimpleStorageProject is the active project.

Launching 'ganache-cli --chain.vmErrorsOnRPCResponse true --server.port 8545 --miner.blockGasLimit 12000000 --wallet.totalAccounts 10 --hardfork istanbul --wallet.mnemonic brownie'...

Running 'scripts/deploy.py::main'...
Hello!
Terminating local RPC client...
COMMENT
```

## 测试

```bash
brownie test [--network xxx] [--pdb]  # 加pdb可以进入调试模式（连接Infura节点时不支持），quit()退出
```

### Fixture

Brownie集成了Pytest作为测试框架，内置了一些Fixture方便与智能合约交互

```python
"""
比如调用accounts时，直接将同名参数作为Fixture传入即可使用
不需要再from brownie import accounts
"""
def test_transfer(Token, accounts):
    pass
```

### 隔离测试

可以使用以下Fixture实现隔离测试

- `module_isolation` 运行测试模块前后重置本地链
- `fn_isolation` 运行测试前快照当前状态，测试结束后恢复

```python
import pytest
from brownie import accounts


# 可以自定义公共Fixture减少重复事务
@pytest.fixture(scope="module")
def token(Token):
    yield Token.deploy("Test Token", "TST", 18, 1e20, {'from': accounts[0]})


def test_transferFrom(fn_isolation, token):
    token.approve(accounts[1], 6e18, {'from': accounts[0]})
    token.transferFrom(accounts[0], accounts[2], 5e18, {'from': accounts[1]})

    assert token.balanceOf(accounts[2]) == 5e18
    assert token.balanceOf(accounts[0]) == 9.5e19
    assert token.allowance(accounts[0], accounts[1]) == 1e18


def test_balance_allowance(fn_isolation, token):
    assert token.balanceOf(accounts[0]) == 1e20
    assert token.allowance(accounts[0], accounts[1]) == 0
```

## 包管理器

Docs: <https://eth-brownie.readthedocs.io/en/stable/package-manager.html>

```bash
# 从Github安装
brownie pm install OpenZeppelin/openzeppelin-contracts@3.0.0

# 从ethPM(以太坊包管理器)安装
$ brownie pm install ethpm://zeppelin.snakecharmers.eth:1/math@1.0.0

# 查看已安装包
brownie pm list

<<'COMMENT'
Brownie v1.19.2 - Python development framework for Ethereum

The following packages are currently installed:

smartcontractkit
 └─smartcontractkit/chainlink-brownie-contracts@0.5.1

OpenZeppelin
 └─OpenZeppelin/openzeppelin-contracts@4.8.0
COMMENT
```

## 账户管理

### 测试账户

使用本地环境默认生成10个测试账户

```python
from brownie import accounts
# 直接使用即可
account = accounts[0]
```

### 本地账户

通过托管节点连接远程网络时，因为是公网，所以并不会像本地开发一样自动创建测试账户，需要自己添加账户，Local accounts are stored in encrypted JSON files known as [keystores](https://julien-maffre.medium.com/what-is-an-ethereum-keystore-file-86c8c5917b97).

- 添加 Local accounts

```bash
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

```bash
brownie accounts delete <id>
```

- 查看 Local accounts

```bash
brownie accounts list

<<'COMMENT'
Found 1 account:
 └─demo: 0x28Fab2EbF6eA4D749F...71556F957CE56Ce05Cf
COMMENT
```

- 使用 Local accounts

Local accounts 账户不能直接使用，必须先unlock

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

### 签名消息

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
