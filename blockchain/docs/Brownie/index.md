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
