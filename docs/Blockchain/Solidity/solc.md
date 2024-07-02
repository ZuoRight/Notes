# 编译器 Solc

EVM（以太坊虚拟机）是智能合约的运行环境，合约之间是完全隔离的

EVM 无法直接执行 Solidity 代码：我们首先需要将其编译成 EVM 字节码。

这就需要用到 Solc（Solidity Compiler）

原生 Solc

> Docs: <https://docs.soliditylang.org/en/latest/installing-solidity.html>

```shell
# docker镜像
docker run ethereum/solc:stable --help

# apt安装
sudo apt update
sudo add-apt-repository ppa:ethereum/ethereum
"""
# 如果提示add-apt-repository命令不存在
sudo apt install software-properties-common

# 如果要移除
add-apt-repository --remove ppa:ethereum/ethereum
"""
sudo apt install solc

# 查看版本
solc --version

# 指定编译版本
solc --evm-version <VERSION> contract.sol
```

solc-bin

可以把不同版本的solc直接下载下来，不用一个个安装

- Docs: <https://docs.soliditylang.org/en/latest/installing-solidity.html#static-binaries>
- 镜像：<https://binaries.soliditylang.org/>
- Github: <https://github.com/ethereum/solc-bin/>

## 版本管理

`solc-select`: <https://github.com/crytic/solc-select>

solc 版本管理，类似于 Java 的版本管理工具 jenv 或 nodejs 的版本管理工具 nvm

```shell
pip3 install solc-select  # 安装

solc-select install  # 查看可安装的solc版本
solc-select install 0.8.7  # 安装指定版本，存储在：~/.solc-select/artifacts/
solc-select use 0.8.7  # 切换版本
solc-select versions  # 显示已安装版本和当前使用版本

solc --version
# 应该与当前使用版本一致，因为solc是对原生solc的包装
# 如果不一致，可能是安装了非原生solc
```

## solcJS

<https://github.com/ethereum/solc-js>

Uses the Emscripten compiled Solidity found in the solc-bin

```shell
# solcjs
sudo npm install solc

# 命令是solcjs，选项与solc也不兼容，比原生solc功能少，不适用于geth等工具
solcjs --help
socljs --version
```

## solcPython

Python wrapper around the solc Solidity compiler.

主要用于集成到Python环境用于下载和编译Solidity

- ethereum/`py-solc` <https://github.com/ethereum/py-solc>
- iamdefinitelyahuman/`py-solc-x` Forked from py-solc
- crytic/`Crytic-compile` <https://github.com/crytic/crytic-compile>

### py-solc-x

Docs: <https://solcx.readthedocs.io/en/latest/>

`pip install py-solc-x`

```python
from solcx import compile_standard, install_solc

with open("./SimpleStorage.sol", "r") as file:
    simple_storage_file = file.read()

# 首次运行会先安装对应版本的Solidity，安装路径：~/.solcx/
install_solc("0.8.7")

compiled_sol = compile_standard(
    {
        "language": "Solidity",
        "sources": {"SimpleStorage.sol": {"content": simple_storage_file}},
        "settings": {
            "outputSelection": {
                "*": {"*": ["abi", "metadata", "evm.bytecode", "evm.sourceMap"]}
            }
        }
    },
    solc_version="0.8.7",
)

# 编译后生成ABI等合约元信息，存储到Json文件中
with open("compiled_code.json", "w") as file:
    json.dump(compiled_sol, file)

# 从编译后的元信息中读取合约ABI和字节码
abi = compiled_sol["contracts"]["SimpleStorage.sol"]["SimpleStorage"]["abi"]
bytecode = compiled_sol["contracts"]["SimpleStorage.sol"]["SimpleStorage"]["evm"]["bytecode"]["object"]
```
