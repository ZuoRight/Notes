# Random

## 链上伪随机数

使用哈希函数以上生成随机数的参数都是公开且可以预测的，所以这种方法是不安全的。

```js
function getRandomOnchain() public view returns(uint256) {
    bytes32 randomBytes = keccak256(
            abi.encodePacked(
                block.number,
                msg.sender,
                blockhash(block.timestamp - 1)
            )
        );
    return uint256(randomBytes)
}
```

## VRF

Chainlink 提供了可验证随机函数(Verifiable Randomness Function)

![20230129205531](https://image.zuoright.com/20230129205531.png)

- V1版本：<https://docs.chain.link/vrf/v1/introduction>
- V2版本：<https://docs.chain.link/vrf/v2/introduction>

V2 版本取代并增强了 V1 版本，但 V1 版本仍可测试使用，示例如下

```js
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

import "@chainlink/contracts/src/v0.8/VRFConsumerBase.sol";

/**
 * Code: https://github.com/smartcontractkit/chainlink-mix/blob/b0ffaff2a53e21a8d6113af4e55bafb471d7a371/contracts/VRFConsumer.sol
 * Video: https://www.youtube.com/watch?v=JqZWariqh5s&ab_channel=Chainlink
 * Get testnet LINK: https://faucets.chain.link/
 */

contract RandomNumberConsumer is VRFConsumerBase {
    event RequestFulfilled(bytes32 requestId, uint256 randomness);

    bytes32 internal keyHash;
    uint256 internal fee;
    uint256 public randomResult;

    /**
     * Constructor inherits(继承) VRFConsumerBase
     *
     * Network: Goerli
     * Chainlink VRF Coordinator address: 0x2bce784e69d2Ff36c71edcB9F88358dB0DfB55b4
     * LINK token address: 0x326C977E6efc84E512bB9C30f76E30c160eD06FB
     * Key Hash: 0x0476f9a745b61ea5c0ab224d3a6e4c99f0b02fce4da01143a4f70aa80ae76e8a
     */
    constructor()
        VRFConsumerBase(
            0x2bce784e69d2Ff36c71edcB9F88358dB0DfB55b4, // VRF Coordinator 地址
            0x326C977E6efc84E512bB9C30f76E30c160eD06FB // 对应网络的 LINK Token 地址
        )
    {
        keyHash = 0x0476f9a745b61ea5c0ab224d3a6e4c99f0b02fce4da01143a4f70aa80ae76e8a; // 随机公钥
        fee = 0.1 * 10 ** 18; // VRF 请求需要消耗 0.1 LINK (Varies by network)
    }

    /**
     * Requests randomness
     * 
     * 在返回随机数前不要重复请求
     */
    function getRandomNumber() public returns (bytes32 requestId) {
        // 需要向部署后的合约中充值足够的LINK（确认钱包中的LINK币合约地址是否与上面的一致）
        require(LINK.balanceOf(address(this)) >= fee, "Not enough LINK - fill contract with faucet");
        return requestRandomness(keyHash, fee);
    }

    /**
     * Callback function used by VRF Coordinator
     * 
     * 生成的随机数randomness是uint256的，可能需要自行处理
     */
    function fulfillRandomness(bytes32 requestId, uint256 randomness) internal override {
        randomResult = (randomness % 50) + 1; // 1～50之间的随机数
        emit RequestFulfilled(requestId, randomness);
    }

    // function withdrawLink() external {} - Implement a withdraw function to avoid locking your LINK in the contract
}
```

## RNG

Random Number Generation
