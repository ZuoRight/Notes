# Sign

## 普通签名

参考：<https://github.com/WTFAcademy/WTF-Ethers/tree/main/18_Signature>

```js
const provider = new ethers.JsonRpcProvider(Infura_URL);
const privateKey = '0x227dbb8586...75d593b6f2b';
const wallet = new ethers.Wallet(privateKey, provider);

// 交易信息经过Keccak256哈希，获得消息摘要
// 等效于Solidity中的keccak256(abi.encodePacked(account, tokenId))
const account = "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4"
const tokenId = "0"
const msgHash = ethers.solidityPackedKeccak256(
    ['address', 'uint256'],
    [account, tokenId]
)
console.log(`msgHash：${msgHash}`)

// 使用钱包对消息摘要签名
// signMessage()函数进行符合EIP191标准的签名：避免用户误签恶意交易，在消息前加上"\x19Ethereum Signed Message:\n32"字符，再做一次keccak256哈希得到以太坊签名消息，然后再签名
const messageHashBytes = ethers.getBytes(msgHash)
const signature = await wallet.signMessage(messageHashBytes);
console.log(`签名：${signature}`)
```

## EIP712 标准签名

参考：<https://github.com/WTFAcademy/WTF-Ethers/tree/main/26_EIP712>

EIP712 提供了一种更高级、更安全的签名方法。

当支持 EIP712 的 Dapp 请求签名时，钱包会展示签名消息的原始数据，用户可以在验证数据符合预期之后签名

- 创建 EIP712 Domain

```js
let contractName = "EIP712Storage"  // 合约名
let version = "1"  // 版本，通常约定为1
let chainId = "1"
let contractAddress = "0xf8e81D47203A594245E36C48e151709F0C19fBe8"  // 验证签名的合约地址

const domain = {
    name: contractName,
    version: version,
    chainId: chainId,
    verifyingContract: contractAddress,
};
```

- 创建类型化数据，Storage

```js
let spender = "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4"
let number = "100"

// 声明类型
const types = {
    Storage: [
        { name: "spender", type: "address" },
        { name: "number", type: "uint256" },
    ],
};

// 包含数据
const message = {
    spender: spender,
    number: number,
};
```

- 调用 signTypedData() 签名

```js
const signature = await wallet.signTypedData(domain, types, message);
`
0xdca07f0c1dc70a4f9746a7b4be145c3bb8c8503368e94e3523ea2e8da6eba7b61f260887524f015c82dd77ebd3c8938831c60836f905098bf71b3e6a4a09b7311b
`
```

- 合约侧验证签名

```js
// 验证 EIP712 签名，从签名和消息复原出 signer 地址
let eip712Signer = ethers.verifyTypedData(domain, types, message, signature)
`
0x5B38Da6a701c568545dCfcB03FcB875f56beddC4
`
```

## 连接 MetaMask

参考：<https://github.com/WTFAcademy/WTF-Ethers/tree/main/ET01_Metamask>

首先需要安装 MetaMask 浏览器插件，一定要到官网下载：<https://metamask.io/download/>

安装后浏览器会给每个页面注入一个 `window.ethereum` 对象，用于和钱包交互

Ether.js 提供了 `BrowserProvider` 对象封装了一个标准的 Web3 Provider 与其交互

```js
const provider = new ethers.BrowserProvider(window.ethereum)

// 读取钱包地址
const accounts = await provider.send("eth_requestAccounts", []);
// 获取ChainID
const { chainId } = await provider.getNetwork()
// 读取钱包ETH余额
const signer = await provider.getSigner()
const balance = await provider.getBalance(signer.getAddress());
```

## 使用 Metamask 签名授权登陆

参考：<https://github.com/WTFAcademy/WTF-Ethers/tree/main/ET02_SignInWithEthereum>

## 多签

为了避免一个私钥的丢失导致地址的资金丢失，引出了多重签名机制，可以实现分散风险的功能。

假设N个人分别持有N个私钥，需要要其中M个人同意签名才可以动用某个“联合地址”的资金

最常见的多重签名是2-3类型。例如，一个提供在线钱包的服务，为了防止服务商盗取用户的资金，可以使用2-3类型的多重签名地址，服务商持有1个私钥，用户持有两个私钥，一个作为常规使用，一个作为应急使用。这样，正常情况下，用户只需使用常规私钥即可配合服务商完成正常交易，服务商因为只持有1个私钥，因此无法盗取用户资金。如果服务商倒闭或者被黑客攻击，用户可使用自己掌握的两个私钥转移资金。
