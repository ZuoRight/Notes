# 引言

## HD 钱包原理

![20231027231935](https://image.zuoright.com/20231027231935.png)

参考文章：<https://wolovim.medium.com/ethereum-201-hd-wallets-11d0c93c87f7>

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
