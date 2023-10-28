# 签名脚本

- EIP712 签名脚本: <https://github.com/WTFAcademy/WTF-Ethers/tree/main/26_EIP712>
- 连接Metamask：<https://github.com/WTFAcademy/WTF-Ethers/tree/main/ET01_Metamask>
- 实现Metamask签名授权登陆：<https://github.com/WTFAcademy/WTF-Ethers/tree/main/ET02_SignInWithEthereum>

## 多重签名

为了避免一个私钥的丢失导致地址的资金丢失，引出了多重签名机制，可以实现分散风险的功能。

假设N个人分别持有N个私钥，需要要其中M个人同意签名才可以动用某个“联合地址”的资金

最常见的多重签名是2-3类型。例如，一个提供在线钱包的服务，为了防止服务商盗取用户的资金，可以使用2-3类型的多重签名地址，服务商持有1个私钥，用户持有两个私钥，一个作为常规使用，一个作为应急使用。这样，正常情况下，用户只需使用常规私钥即可配合服务商完成正常交易，服务商因为只持有1个私钥，因此无法盗取用户资金。如果服务商倒闭或者被黑客攻击，用户可使用自己掌握的两个私钥转移资金。
