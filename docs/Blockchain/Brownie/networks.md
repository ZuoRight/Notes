# brownie networks

Brownie 框架默认集成了 Ganache 等，可以通过 `brownie networks ...` 来管理

## 查看网络

```shell
brownie networks list

<<'COMMENT'
The following networks are declared:

Ethereum
  ├─Mainnet (Infura): mainnet
  ├─Ropsten (Infura): ropsten
  ├─Rinkeby (Infura): rinkeby
  ├─Goerli (Infura): goerli
  └─Kovan (Infura): kovan

Ethereum Classic
  ├─Mainnet: etc
  └─Kotti: kotti

Arbitrum
  └─Mainnet: arbitrum-main

......

Binance Smart Chain
  ├─Testnet: bsc-test
  └─Mainnet: bsc-main

......

Development
  ├─Anvil: anvil
  ├─Anvil (Mainnet Fork): anvil-fork
  ├─Hardhat: hardhat
  ├─Hardhat (Mainnet Fork): hardhat-fork
  ├─Geth Dev: geth-dev
  ├─Ganache-CLI: development
  ├─Ganache-CLI (Mainnet Fork): mainnet-fork
  ├─Ganache-CLI (BSC-Mainnet Fork): bsc-main-fork
  ......
  └─Ganache-CLI (Aurora-Mainnet Fork): aurora-main-fork
COMMENT
```

## 新增网络

```shell
brownie networks add [environment] [id] host=[host] [KEY=VALUE, ...]

<<'COMMIT'
# 基本参数
environment 网络类别，比如：Ethereum、Arbitrum、Development
id 网络唯一标识，比如mainnet-fork
host 节点地址
name 网络名称，可选，默认同id
timeout RPC调用响应时间，可选，默认30

# 实时网络参数
chain_id 可以通过<https://chainlist.org>查看
explorer 可选

# 本地网络参数
cmd 启动命令
port 如果没有包含在host中则需要单独指定
fork 从live network分叉
chain_id 默认1337或1，如果fork的其他网络则默认与其相同
block_time 出块时间，默认瞬间出块
mnemonic 生成本地账户时使用的助记符，默认随机
accounts 生成的的账户数，默认10个
default_balance 账户初始余额，默认100ETH
COMMIT
```

- add

```shell
brownie networks add Ethereum ganache-local host=http://127.0.0.1:8545 chainid=1337

<<'COMMENT'
SUCCESS: A new network 'ganache-local' has been added
  └─ganache-local
    ├─id: ganache-local
    ├─chainid: 1337
    └─host: http://127.0.0.1:8545
COMMENT


# 然后即可以指定自己构建的网络
brownie run scripts/deploy.py --network ganache-local

<<'COMMENT'
需要先启动Ganache UI
部署记录会保存到build/deployments下的1337文件和map.json中1337字段
每次重启Ganache UI后再部署需要先删除旧的部署记录
COMMENT
```

- add fork

```shell
brownie networks add development mainnet-fork-dev \
    cmd=ganache-cli \
    host=http://127.0.0.1:8545 \
    # fork='https://mainnet.infura.io/v3/$WEB3_INFURA_PROJECT_ID' \
    fork='https://eth-mainnet.g.alchemy.com/v2/$WEB3_ALCHEMY_PROJECT_ID' \
    port=8545 \
    mnemonic=brownie \
    accounts=10 \

<<'COMMENT'
SUCCESS: A new network 'mainnet-fork-dev' has been added
  └─mainnet-fork-dev
    ├─id: mainnet-fork-dev
    ├─cmd: ganache-cli
    ├─cmd_settings: {'fork': 'https://eth-mainnet.g.alchemy.com/v2/$WEB3_ALCHEMY_PROJECT_ID', 'accounts': 10, 'mnemonic': 'brownie', 'port': 8545}
    └─host: http://127.0.0.1:8545
COMMENT


# 使用
brownie run scripts/deploy.py --network mainnet-fork-dev
<<'COMMENT'
FundmeBrownieProject is the active project.

Launching 'ganache-cli \
--chain.vmErrorsOnRPCResponse true \
--wallet.totalAccounts 10 \
--fork.url https://eth-mainnet.g.alchemy.com/v2/GU7-1AJnjl8ftDhzoXu7GgsQiP8SbdCt \
--wallet.mnemonic brownie --server.port 8545 --hardfork istanbul'...

Running 'scripts/deploy.py::main'...
Transaction sent: 0xcd7be787855407afd950e1a5e59e84d8191cbab55aa55e7b2568729a7328cd60
  Gas price: 0.0 gwei   Gas limit: 30000000   Nonce: 6
  FundMe.constructor confirmed   Block: 16325839   Gas used: 421924 (1.41%)
  FundMe deployed at: 0x9E4c14403d7d9A8A782044E86a93CAE09D7B2ac9

Contract deployed to 0x9E4c14403d7d9A8A782044E86a93CAE09D7B2ac9
Terminating local RPC client...
COMMENT
```

## 设置默认网络

```yaml
# brownie-config.yaml
networks:
    default: goerli
```

## 启动

- CLI

```shell
brownie --network mainnet-fork
```

- brownie.network

```python
from brownie import network

network.connect('goerli')
network.disconnect()

network.is_connected()  # True
network.show_active()  # 'goerli'，默认是'development'
```

## 使用托管节点

- 查看托管节点提供商

```shell
brownie networks list_providers

<<'COMMENT'
The following providers are declared:
  ├─dict_keys(['infura', 'alchemy']):
COMMENT


# 或者
brownie networks list_providers True

<<'COMMENT'
The following providers are declared:
  ├─provider: infura:
  ├─   host: {'host': 'https://{}.infura.io/v3/$WEB3_INFURA_PROJECT_ID'}:
  ├─provider: alchemy:
  ├─   host: {'host': 'https://eth-{}.alchemyapi.io/v2/$WEB3_ALCHEMY_PROJECT_ID'}:
COMMENT
```

- 添加/更新提供商

```shell
brownie networks update_provider alchemy https://eth-{}.alchemyapi.io/v2/$WEB3_ALCHEMY_PROJECT_ID
```

- 更换提供商

可以将`brownie networks list`中标识提供商的网络更换为另一个提供商

```shell
# 单独修改
brownie networks modify goerli provider=alchemy

<<'COMMENT'
SUCCESS: Network 'Mainnet (Infura)' has been modified
  └─Mainnet (Infura)  注意：修改完后name是不变的
    ├─id: mainnet
    ├─chainid: 1
    ├─explorer: https://api.etherscan.io/api
    ├─host: https://mainnet.infura.io/v3/$WEB3_INFURA_PROJECT_ID
    ├─multicall2: 0x5BA1e12693Dc8F9c48aAD8770482f4739bEeD696
    └─provider: alchemy  # 但提供商确实已经变了
COMMENT

# 批量修改（设置提供商）
brownie networks set_provider alchemy  # 修改完后name也跟着变了
```
