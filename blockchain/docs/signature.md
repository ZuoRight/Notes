# 加密算法

![20220103171609](http://image.zuoright.com/20220103171609.png)

## 哈希摘要算法

Hash，一般译作哈希，或者散列

就是把任意长度的输入数据压缩转换成固定长度的散列值，无法解密

两个特性：

- 易变性：两个不同的输入不会得到相同的输出
- 不可逆性，无法从输出碰撞出输入

常见的哈希算法有：MD5，SHA，RIPEMD160

### MD5

可以把任意长度的明文转化成一串128bits的哈希值

已被证明不具有强抗碰撞性

### SHA

Secure Hash Algorithm，哈希函数族目前有三代标准

- SHA-1 已被证实不具备强抗碰撞性
- SHA-2 目前用的最多，包含：SHA-224, SHA-256, SHA-384, SHA-512等
- SHA-3

### RIPEMD160

比特币生成地址就用到了这个算法

## 对称加密算法

加密与解密时采用同一个密钥，效率高，但安全系数较低。

常见的有：DES，3DES，AES等

## 非对称加密算法

加密与解密时采用不同的密钥，效率低，但安全系数较高。

常见的有：RSA，ECC等

非对称加密算法将密钥分成公钥和私钥，公钥可以完全公开，无须保证传输安全。私钥由用户自行保管，不参与任何通信传输。

- 加密：公钥加密，私钥解密，可逆
- 签名：私钥加密，公钥解密，不可逆

将数据用公钥加密，发送给私钥所有者，数据无法被他人解密，但可能被他人篡改，因为每个人都可以用公钥加密数据。

用私钥加密一段内容作为签名，每个拿到公钥的人都可以验证私钥所有者的身份，防止数据被篡改。

于是可以将加密与签名两种方式结合起来使用，通常用于传输对称加密的密钥。

![加密](http://image.zuoright.com/加密.png)

### 私钥 => 公钥

- 私钥：64 个十六进制字符（32Bytes, 256bits）
- 公钥：128 个十六进制字符（68Bytes, 512bits）

根据椭圆曲线算法可通过私钥生成公钥

```python
p = curve_point_from_int(private_key)
public_key_bytes = serialize_curve_point(p)
print(f'public key: 0x{public_key_bytes.hex()}')  # public key: 0x024c8f4044470bd42b81a...
```

然后生成一个收款地址，a public address is the last 20 Bytes of the Keccak-256 hash of the public key points（取64Bytes的后20Bytes）

```python
from eth_utils import keccak

digest = keccak(x.to_bytes(32, 'big') + y.to_bytes(32, 'big'))
address = '0x' + digest[-20:].hex()
print(f'public address: {address}')  # public address: 0xbbec2620cb01adae3f96e1fa39f997f06bfb7ca0
```

## 数字签名

数字签名（也叫数字摘要，类似指纹），作用：防伪造，防篡改，防抵赖。

通常由非对称加密算法和哈希算法相结合生成

- EIP712 签名脚本: <https://github.com/WTFAcademy/WTF-Ethers/tree/main/26_EIP712>
- 连接Metamask：<https://github.com/WTFAcademy/WTF-Ethers/tree/main/ET01_Metamask>
- 实现Metamask签名授权登陆：<https://github.com/WTFAcademy/WTF-Ethers/tree/main/ET02_SignInWithEthereum>

### RSA

### DSA

### ECDSA

椭圆曲线数字签名算法

## 多重签名

为了避免一个私钥的丢失导致地址的资金丢失，引出了多重签名机制，可以实现分散风险的功能。

假设N个人分别持有N个私钥，需要要其中M个人同意签名才可以动用某个“联合地址”的资金

最常见的多重签名是2-3类型。例如，一个提供在线钱包的服务，为了防止服务商盗取用户的资金，可以使用2-3类型的多重签名地址，服务商持有1个私钥，用户持有两个私钥，一个作为常规使用，一个作为应急使用。这样，正常情况下，用户只需使用常规私钥即可配合服务商完成正常交易，服务商因为只持有1个私钥，因此无法盗取用户资金。如果服务商倒闭或者被黑客攻击，用户可使用自己掌握的两个私钥转移资金。
