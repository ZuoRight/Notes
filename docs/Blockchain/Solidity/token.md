# 代币 Token

## ERC20

来自 2015 年 11 月 V 神参与的 EIP20，它实现了代币转账的基本逻辑，现在已是使用最广泛的代币标准

- `IERC20` EIP 定义的 ERC20 标准接口，规定了 ERC20 代币需要实现的函数和事件
- `ERC20` IERC20 接口的基本实现，包括名称、符号和小数可选标准扩展等
- `IERC20Metadata` 扩展的 ERC20 接口，包括名称、符号和小数功能
- `SafeERC20` 接口的包装器，无需处理布尔返回值，`using SafeERC20 for IERC20;`

只要合约实现了 IERC20 接口列出的所有方法，就满足了 ERC20 标准，至于合约逻辑如何实现没有强制要求。

之所以需要定义接口，是因为有了规范后，就存在所有的 ERC20 代币都通用的函数名称，输入参数，输出参数。

在接口函数中，只需要定义函数名称，输入参数，输出参数，并不关心函数内部如何实现。

由此，函数就分为内部和外部两个内容，一个重点是实现，另一个是对外接口，约定共同数据。

这就是为什么需要 `ERC20.sol` 和 `IERC20.sol` 两个文件实现一个合约。

可以使用 OpenZeppelin 等提供的 API 来快速构建 ERC：<https://docs.openzeppelin.com/contracts/4.x/api/token/erc20>

视频参考：<https://wtf.academy/solidity-application/ERC20/>

```solidity
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

// 可升级合约
import "@openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol";
import "@openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol";
import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
```

```solidity
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

### 关于 WETH & WBTC

<https://wtf.academy/solidity-application/WETH/>

ERC20 标准的出现简化了代币发行，但是 ETH 本身并不符合 ERC20 标准，为了提高区块链间的互操作，使 ETH、BTC 等可用于 DApps，于是就出现了 WETH、WBTC 等符合 ERC20 标准的 Wrapped 币用于跨链，可以与 ETH、BTC 1:1 兑换

## ERC777

更丰富的可替代代币标准，兼容 ERC20

## ERC721

NFT(non-fungible tokens) 不可替代代币

通常用于收藏品和游戏

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/utils/Counters.sol";

contract GameItem is ERC721URIStorage {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIds;

    constructor() ERC721("GameItem", "ITM") {}

    // NFT 是不可分割的，所以他不支持精度设置
    function awardItem(address player, string memory tokenURI) public returns (uint256) {
        uint256 newItemId = _tokenIds.current();
        _mint(player, newItemId);
        _setTokenURI(newItemId, tokenURI);

        _tokenIds.increment();
        return newItemId;
    }
}
```

## ERC1155

一种新的多代币标准，允许单个合约代表多个可替代和不可替代的代币，以及用于提高 Gas 效率的批量操作
