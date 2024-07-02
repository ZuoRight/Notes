# 区块浏览器

从技术上看，区块浏览器的主要工作就是把区块扫描到数据库服务器中，然后搭建一个 Web 访问服务，用户只需要输入交易哈希或者区块哈希，即可查询到交易是否已经被打包和确认。

![20231026220750](https://image.zuoright.com/20231026220750.png)

- 多链：[OKLink](https://www.oklink.com/all-chain)
    - <https://www.oklink.com/btc>
    - <https://www.oklink.com/eth>
- Ethereum：<https://etherscan.io/>
- BSC 币安智能链：<https://bscscan.com/>
- Polygon：<https://polygonscan.com/>
- Arbitrum：<https://arbiscan.io/>

Etherscan 参考官方文档及以下文章

- <https://metatraining.buidlerdao.xyz/buidlerdao/6-2-Etherscan-07a5de0512ab423ebc172b2c9653a03b>
- <https://x.com/jianshubiji/status/1559842293070581760>

可查询的信息类型：交易哈希、Address、Contract

![20240702230545](https://image.zuoright.com/20240702230545.png)

## 查询交易哈希

<https://goerli.etherscan.io/tx/0xd419b5789358d5b5f7a73b18c9f2aadafc1b7cdbd362072b9d7804e6cd6b7365>

每一笔交易，都会生成一个较长的 Hash 与之一一对应，我们称之为 Transaction Hash（交易哈希，缩写为 Txn Hash）

![20240702230309](https://image.zuoright.com/20240702230309.png)

## 查询 Address

<https://goerli.etherscan.io/address/0x0F5e62Ab14049f08C80c5b1bcc262b03f8350887>

![20240702230356](https://image.zuoright.com/20240702230356.png)

查看具体分类转账数量及详情，接收 ERC20 代币，只会在发起方有记录，接收方无记录

![20240702230449](https://image.zuoright.com/20240702230449.png)

## 查看 Contract

<https://goerli.etherscan.io/address/0xA2025B15a1757311bfD68cb14eaeFCc237AF5b43>

![20240702230644](https://image.zuoright.com/20240702230644.png)

Token Tracker

<https://etherscan.io/token/0x06450dEe7FD2Fb8E39061434BAbCFC05599a6Fb8>

![20240702230715](https://image.zuoright.com/20240702230715.png)

## 验证合约

![20240702231305](https://image.zuoright.com/20240702231305.png)

![20240702231318](https://image.zuoright.com/20240702231318.png)

手动验证的话，需要自己提交合约代码，如果合约中 `import` 了 `chainlink` 等第三方代码，需要将依赖的代码也放进来，比较麻烦

所以可以通过注册 Etherscan 账号，创建 API，然后部署合约时允许发布源码，来自动验证

```python
# publish_source表示发布合约源码
fund_me = FundMe.deploy({"from": account}, publish_source=True)

# 打印信息
"""
brownie run scripts/deploy.py --network goerli
Brownie v1.19.2 - Python development framework for Ethereum

FundmeBrownieProject is the active project.

Running 'scripts/deploy.py::main'...
Transaction sent: 0xbf01618fc32efef8996a97b8726f9a960c6d72a0537fc778835abad9a9884f54
  Gas price: 2.0652e-05 gwei   Gas limit: 451951   Nonce: 32
  FundMe.constructor confirmed   Block: 8242835   Gas used: 410865 (90.91%)
  FundMe deployed at: 0x5398F9A5918906866d8275Bc26839259a31ce80d

# 这一步需要借助科学上网
Waiting for https://api-goerli.etherscan.io/api to process contract...
Verification submitted successfully. Waiting for result...
Verification complete. Result: Pass - Verified
Contract deployed to 0x5398F9A5918906866d8275Bc26839259a31ce80d
"""
```

![20240702231609](https://image.zuoright.com/20240702231609.png)
