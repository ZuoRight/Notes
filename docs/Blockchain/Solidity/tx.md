# 交易 Transaction

[Understanding an Ethereum Transaction](https://info.etherscan.com/understanding-an-ethereum-transaction/)

## 账户和交易类型

在以太坊网络中，主要有两种类型的账户

- 外部账户（EOA, Externally Owned Accounts）由用户通过私钥控制，地址通过 HD 钱包生成
- 合约账户（Contract Accounts）由合约代码控制，地址在部署合约时生成

任何在以太坊上发生的交易必须由 EOA 发起。

不过合约开发者会通过添加修饰符（如 `onlyOwner` 或 `require`）来限制特定函数的访问，使得只有合约的拥有者或具有特定角色的账户才能调用这些函数。

- 内部交易：合约间调用，不会在以太坊的主区块链上独立记录，而是作为原始交易（由 EOA 发起）的一部分结果
- 合约创建交易，部署合约，生成合约账户地址
- 合约调用交易，执行部署后合约的某个方法，以此修改合约状态，触发合约间调用等
- 普通交易（Ether Transfer）EOA 向另一个 EOA 或 合约账户发送 ETH 代币，不涉及执行智能合约代码
- 代币转移（Token Transfer）ERC20 或 ERC721 等 Token Txns，通过调用智能合约的特定函数（如 `transfer` 或 `transferFrom`）来实现
- DeFi 操作，DeFi 平台的借贷、Swap、流动性提供、Flash Loan 等，通常涉及复杂的智能合约交互和内部交易

![20230508165138](https://image.zuoright.com/20230508165138.png)

## Sending Ether

[Sending Ether (transfer, send, call)](https://solidity-by-example.org/sending-ether/)

### send

- `接收方地址.transfer(发送ETH数额)`

消耗上限为 2300 Gas，失败报错并回滚(revert)

```solidity
payable(msg.receiver).transfer(address(this).balance);

function transferETH(address payable _to, uint256 amount) external payable{
    _to.transfer(amount);
}
```

- `接收方地址.send(发送ETH数额)`

消耗上限为 2300 Gas，失败不会自动回滚，需要通过 require 语句判断返回 bool 值是否 false

貌似很少人使用这个方法

```solidity
bool success = payable(msg.receiver).send(address(this).balance);

function sendETH(address payable _to, uint256 amount) external payable{
    bool success = _to.send(amount);
    // 处理返回值，如果失败，revert交易并发送error
    if(!success){
        revert SendFailed();
    }
}
```

- `接收方地址.call{value: 发送ETH数额}("")`

> call 属于底层方法，可以调用几乎所有 solidity 函数，也可以用来转账

用 call 转账时没有 Gas 上限，失败返回 false

call 是官方推荐的通过触发 fallback 或 receive 函数发送 ETH 的方法

但是不推荐用 call 调用另一个合约，因为会转移权限

```solidity
// 两个返回值：(bool <调用是否成功true/false>, bytes <调用函数时接收函数的返回值>)
(bool success, ) = payable(msg.receiver).call{
        value: address(this).balance,
        gas: 5000
    }("");  // ("")里是被调用的函数，留空表示不调用任何函数，仅用来转账（所以才需要加{}）

function callETH(address payable _to, uint256 amount) external payable{
    (bool success,) = _to.call{value: amount}("");
    // 处理返回值，如果失败，revert交易并发送error
    if(!success){
        revert CallFailed();
    }
}
```

### receive

- `receive() external payable`
- `fallback() external payable`

不需要 function 关键字声明

```solidity
/**
Which function is called, fallback() or receive()?
        send Ether
            |
      msg.data is empty?
          /     \
        yes     no
        /         \
receive() exists?  fallback()
        /   \
    yes      no
    /          \
receive()   fallback()
*/

contract ReceiveEther {
    // 定义一个事件
    event Received(address Sender, uint Value);

    // Function to receive Ether. msg.data must be empty
    // 必须由 external 和 payable 修饰
    // 不能有任何参数，也不能返回任何值
    receive() external payable {
        // 接收ETH时触发Received事件
        emit Received(msg.sender, msg.value);
    }

    // Fallback function is called when msg.data is not empty
    // 在调用合约不存在的函数时被触发
    // 可用于接收ETH，也可以用于代理合约
    // 必须由external修饰，一般也会用payable修饰
    fallback() external payable {
        emit fallbackCalled(msg.sender, msg.value, msg.data);
    }

    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}
```

## 代币批准

参考：<https://blog.uniswap.org/permit2-and-universal-router>

### Approve

规范代币批准的方法最初在 EIP-20 中定义，但存在一些弱点，用户必须为他们想要使用的每个应用程序发送批准交易（Approve），应用程序会要求用户批准最大限额，为了方便和节省 Gas，通常会允许一个比较大的额度，从而使应用程序可以无限期地访问钱包的全部代币余额。

对于 ERC-20 代币来说，会有两个账本，一个维护用户余额（balance），另一个维护授权列表和授权额度（allowance）

![20230603160539](https://image.zuoright.com/20230603160539.png)

`approve()` 函数本身是根据 `msg.sender` 定义的，初始操作必须由 EOA 执行，如果用户需要与智能合约交互，那么他们需要进行 2 笔交易（`approve` 和 `transferFrom`）

![20230603160752](https://image.zuoright.com/20230603160752.png)

### Permit

<https://eips.ethereum.org/EIPS/eip-2612>

EIP-2612 通过新函数 `permit` 扩展了 EIP-20 标准，该函数允许用户使用签名消息而不是通过 `msg.sender` 修改授权额度（allowance）映射

用户可以通过在交易中附加签名的 Permit 信息来与应用程序合约进行交互，而无需事先批准。

为了改善用户体验，签名数据的结构遵循 EIP-712，它已经在主要 RPC 提供商中得到广泛采用

但在这之前推出的代币都不支持 Permit 功能，而且较新的代币也不一定会采用它。

### Permit2

Uniswap 开发了 Permit2 合约，通过为任何 ERC20 代币引入基于签名的批准和传输来进一步迭代代币批准机制，无论 EIP-2612 是否支持。

Permit2 还附带了许多令人兴奋的功能，可以解锁更安全的令牌审批选项，并在任何集成应用程序中实现更一致的用户体验。

Permit2 合约获得用户的批准后，可用于向其他智能合约授予子批准。

这可以通过 `Permit2.approve()` 函数来完成，该函数的工作方式与 ERC20 代币上的 `approve()` 函数类似。

也可以通过 `Permit2.permit()` 函数来完成，其工作原理类似于 EIP2612 `permit()` 函数。

## 签名

<https://www.cyfrin.io/blog/understanding-ethereum-signature-standards-eip-191-eip-712>

### EIP191 标准签名

参考：<https://github.com/WTFAcademy/WTF-Ethers/tree/main/18_Signature>

传统的以太坊交易签名方法，使用双椭圆曲线数字签名算法（ECDSA）。

签名的消息是向用户显示的不透明的十六进制字符串

每个以太坊交易，包括发送 ETH 或调用智能合约（如 ERC-20 的 `approve` 函数）都需要此类签名

```solidity
// 初始化提供者和钱包
const provider = new ethers.JsonRpcProvider(Infura_URL);
const privateKey = '0x227dbb8586...75d593b6f2b';
const wallet = new ethers.Wallet(privateKey, provider);

// 交易信息经过 Keccak256 哈希，获得消息摘要
// 等效于 Solidity 中的 keccak256(abi.encodePacked(account, tokenId))
const account = "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4"
const tokenId = "0"
const msgHash = ethers.solidityPackedKeccak256(
    ['address', 'uint256'],
    [account, tokenId]
)
console.log(`msgHash：${msgHash}`)

// 使用钱包对消息摘要签名
// signMessage() 函数进行符合 EIP191 标准的签名：避免用户误签恶意交易，
//     在消息前加上 "\x19Ethereum Signed Message:\n32" 字符，再做一次 keccak256 哈希得到以太坊签名消息，然后再签名
const messageHashBytes = ethers.getBytes(msgHash)
const signature = await wallet.signMessage(messageHashBytes);
console.log(`签名：${signature}`)
```

### EIP712 标准签名

- <https://eips.ethereum.org/EIPS/eip-712>
- <https://github.com/WTFAcademy/WTF-Ethers/tree/main/26_EIP712>

EIP712 是对类型化和结构化数据（而不仅仅是字节串）进行哈希和签名的标准，提供了一种更高级、更安全的签名方法。

当支持 EIP712 的 DApp 请求签名时，钱包会展示签名消息的原始数据，用户可以在验证数据符合预期之后签名

旨在以更易于理解的方式显示交易，以便用户在签名之前可以理解和查看。

- 创建 EIP712 Domain

```solidity
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

```solidity
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

```solidity
const signature = await wallet.signTypedData(domain, types, message);
`
0xdca07f0c1dc70a4f9746a7b4be145c3bb8c8503368e94e3523ea2e8da6eba7b61f260887524f015c82dd77ebd3c8938831c60836f905098bf71b3e6a4a09b7311b
`
```

- 合约侧验证签名

```solidity
// 验证 EIP712 签名，从签名和消息复原出 signer 地址
let eip712Signer = ethers.verifyTypedData(domain, types, message, signature)
`
0x5B38Da6a701c568545dCfcB03FcB875f56beddC4
`
```
