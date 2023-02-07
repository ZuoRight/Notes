# Solc

Solc 即 Solidity Compiler

> <https://docs.soliditylang.org/en/v0.8.17/installing-solidity.html>

```bash
# npm安装
sudo npm install -g solc

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
solc --version
```

Python环境用于编译Solidity的工具主要有：

- `py-solc` is Python wrapper around the solc Solidity compiler.
- `py-solc-x` is Forked from py-solc
- `Crytic-compile` <https://github.com/crytic/crytic-compile>

Brownie内置了`Crytic-compile`，加载时会自动编译，Web3需要自己编译

## solc-select

solc 版本管理，类似于 Java 的版本管理工具 jenv 或 nodejs 的版本管理工具 nvm

> <https://github.com/crytic/solc-select>

```bash
pip3 install solc-select  # 安装

solc-select install  # 查看可安装的solc版本
solc-select install 0.8.7  # 安装指定版本，存储在：~/.solc-select/artifacts/
solc-select versions  # 显示已安装版本和当前使用版本
solc-select use 0.8.7  # 切换版本
```

## py-solc-x

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
