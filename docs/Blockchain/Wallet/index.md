# 引言

根据私钥的管理方式可分为

- 中心化钱包：将完整的私钥加密之后管理在中心化的环境中，比如交易所，常见的管理方式有 TEE，KMS 和 CloadHSM
- 去中心化钱包：私钥加密之后存储在用户设备中，私钥丢失无法找回
    - AA 钱包
    - 社交恢复钱包
        - 密钥分片备份
        - 守护者恢复
- 硬件钱包：主要是把私钥管理在离线的硬件设备中，在硬件设备中集成钱包签名算法
- 托管钱包
    - 多签钱包：Gnosis Safe
    - MPC 钱包

## ENS

<https://app.ens.domains/>

类似 DNS 的以太坊域名服务 ENS（Ethereum Name Service）

钱包地址类似于IP，可以绑定域名，方便记忆

比如V神的域名为：[vitalik.eth](https://app.ens.domains/vitalik.eth)

```js
// 将ENS解析为地址
const addressVitalik = await provider.resolveName("vitalik.eth")
```

## Find a Wallet

<https://ethereum.org/en/wallets/find-wallet/#main-content>

最常用的以太坊钱包：[MetaMask](https://metamask.io/)

推荐最好用的多链钱包：OKX Wallet
