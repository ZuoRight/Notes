# Token

## ERC777

更丰富的可替代代币标准，兼容ERC20

## ERC721

NFT(non-fungible tokens) 不可替代代币

通常用于收藏品和游戏

```js
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

一种新的多代币标准，允许单个合约代表多个可替代和不可替代的代币，以及用于提高 gas 效率的批量操作
