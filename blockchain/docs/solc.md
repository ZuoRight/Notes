# Solc

Solc 即 Solidity Compiler

Docs：<https://docs.soliditylang.org/en/v0.8.17/installing-solidity.html>

Python环境用于编译Solidity的工具有：

- `py-solc` is Python wrapper around the solc Solidity compiler.
- `py-solc-x` is Forked from py-solc
- `py-solc-ast` Brownie依赖的编译工具

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
