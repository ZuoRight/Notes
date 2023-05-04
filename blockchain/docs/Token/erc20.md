# ERC20

最广泛的代币标准：<https://docs.openzeppelin.com/contracts/4.x/api/token/erc20>

- `IERC20` EIP定义的ERC20标准接口
- `IERC20Metadata` 扩展的ERC20接口，包括名称、符号和小数功能
- `SafeERC20` 接口的包装器，无需处理布尔返回值，`using SafeERC20 for IERC20;`
- `ERC20` IERC20接口的基本实现，包括名称、符号和小数可选标准扩展等

只要合约实现了IERC20接口列出的所有方法，就满足了ERC20标准，至于合约逻辑如何实现没有强制要求。

```js
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

// 可升级合约
import "@openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol";
import "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";
import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
```

```js
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract BFToken is ERC20 {
    constructor(uint256 initialSupply) ERC20("BugFree", "BF") {
        _mint(msg.sender, initialSupply);  // initialSupply为初始供应量
    }

    // 默认18位精度
    function decimals() public view virtual override returns (uint8) {
        return 18;
    }
}
```
