# Solidity

[官方文档](https://docs.soliditylang.org/en/latest/index.html){ .md-button .md-button--primary }

Solidity 是一种面向对象的高级语言，用于实现智能合约。

> 智能合约是管理以太坊状态下账户行为的程序

## 开发环境

以太坊官方推荐的Remix IDE，集成了Solidity编译器和运行时环境

Docs: <https://remix-ide.readthedocs.io/en/latest/index.html>

在线IDE：<https://remix.ethereum.org>

```Plain text
红色按钮表示可以向合约支付，即payable
橙色按钮表示需要支付Gas
蓝色按钮表示不需要支付Gas
```

### 本地搭建

比如：`VSCode + Solidity插件 + Solc`

> Solc 即 [Solidity Compiler](https://docs.soliditylang.org/en/v0.8.17/installing-solidity.html)

Python环境用于编译Solidity的工具主要有：

- `py-solc` is Python wrapper around the solc Solidity compiler.
- `py-solc-x` is Forked from py-solc

Brownie内置了编译组件，加载时会自动编译，Web3需要自己编译

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

## 规范

代码第一行需要注释智能合约所用的许可证，如果不写编译时会警告

> SPDX-License-Identifier，即遵循SPDX(Software Package Data Exchange)国际标准规范的许可证

语句以分号结尾

```js
// SPDX-License-Identifier: MIT
pragma solidity >=0.8.4 <0.9.0;  // 声明版本，可简写为：^0.8.4，^表示向上兼容

// 创建合约
contract HelloWeb3{
    string public _string = "Hello Web3!";
}
```
