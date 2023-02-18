# Price Feed

<https://docs.chain.link/data-feeds/price-feeds>

```js
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

// 当我们与Chainlink喂价交互时，实际上是从被称之为聚合器(AggregatorV3Interface.sol)的合约中读取最新的数据
import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

contract ChainlinkPriceOracle {
    AggregatorV3Interface internal priceFeed;

    constructor() {
        // 不同网络与交易对价格合约地址查询：https://docs.chain.link/data-feeds/price-feeds/addresses/
        priceFeed = AggregatorV3Interface(0xD4a33860578De61DBAbDc8BFdb98FD742fA7028e); // Goerli: ETH/USD
    }

    function getPrice() public view returns (uint256) {
        // interface AggregatorV3Interface {
        //     function latestRoundData() external view returns (
        //         uint80 roundId,
        //         int answer,
        //         uint startedAt,
        //         uint updatedAt,
        //         uint80 answeredInRound
        //     );
        // }
        // latestRoundData()方法会返回5个变量，实际使用时只需要读取需要的变量，其它留空
        (, int256 price, , ,) = priceFeed.latestRoundData();  // price可能为负数所以使用int

        // 由于处理浮点数会有精度问题，所以智能合约中不用浮点数
        // 比如$ETH如果为$3000，预言机返回的数实际会是300000000000，其中有8位是精度
        // 可以通过priceFeed.decimals()方法获取精度，即小数点(decimals)
        return uint256(price * 1e10);  // 返回的price默认8位精度，补齐为18位精度方便计算，并转换为正整数
    }

    // 使用Price：计算N个ETH等价于多少USD
    function getEthAmountInUsd(uint256 ethAmount) public view returns(uint256) {
        uint256 ethPrice = getPrice();
        // ethPrice和ethAmount相乘精度变为36个0，所以需要消除18个
        uint256 ethAmountInUsd = (ethAmount * 1e18 * ethPrice) / 1e18;  // Wei
        return ethAmountInUsd;
    }

    // 使用Price：计算X个USD等价于多少ETH
    function getUsdAmountInEth(uint256 usdAmount) public view returns(uint256) {
        uint256 ethPrice = getPrice();
        uint256 usdAmountInEth = (usdAmount * 1e18 / ethPrice) * 1e18;  // Wei
        return usdAmountInEth;
    }
}
```
