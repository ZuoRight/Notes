# Cryptography

![20220103171609](http://image.zuoright.com/20220103171609.png)

<https://rakhesh.com/infrastructure/notes-on-cryptography-ciphers-rsa-dsa-aes-rc4-ecc-ecdsa-sha-and-so-on/>

## 哈希算法

Hash，一般译作哈希，或者散列

哈希算法是一个函数，可以将一段任意长度的数据生成独一无二的固定长度（2的N次方）的哈希值（也叫散列值，或摘要）

如果两段输入的哈希结果不同，可以百分百确定两段输入是不同的，如果哈希结果相同，则不输入数据理论上是相同的，但也存在一定的不可靠性

另外与压缩和加密算法不同，哈希算法是不可逆的，无法解密，主要用于摘要

不同 Hash 算法之间区别不大，只是哈希结果不同

比特币产生出新的区块，就是将区块头哈希然后满足一定条件后算出来的

可以参考示例：<https://andersbrownworth.com/blockchain/>

### MD5

MD5 可以把任意长度的明文转化成一串128bits的哈希值，已被证明不具有强抗碰撞性

此外还有 MD2、MD4、MD6等

### SHA

Secure Hash Algorithm，哈希函数族目前有三代标准

- SHA-0
- SHA-1 已被证实不具备强抗碰撞性
- SHA-2 目前用的最多，包含：SHA-224, SHA-256, SHA-384, SHA-512等
- SHA-3

### RIPEMD160

比特币生成地址时使用的算法

### Keccak-256

以太坊钱包地址，就是将公钥经过这个哈希算法，再取后面的20个字节生成的

## 对称加密算法

加密与解密时采用同一个密钥，效率高，但安全系数较低。

通常用于C/S间的流量加密，以及磁盘上的数据加密

常见的算法有：

- DES 数据加密标准，基于此的算法有时称作 DEA
- IDEA 国际数据加密算法
- AES 高级加密标准
- RC4

## 非对称加密算法

<https://andersbrownworth.com/blockchain/public-private-keys/keys>

加密与解密时采用不同的密钥，效率低，但安全系数较高。

非对称加密算法将密钥分成公钥和私钥，针对相同的内容，私钥和公钥可以得到相同的信息

- 私钥：64 个十六进制字符（32Bytes, 256bits），由用户自行保管，不参与任何通信传输
- 公钥：128 个十六进制字符（68Bytes, 512bits），可以完全公开，无须保证传输安全

### 签名

将一段信息进行哈希（比如用keccak256将一笔交易哈希），然后使用私钥给哈希值签名，每个拿到公钥的人都可以验证它

验证通过则说明：

- 信息来源没有问题，因为签名无法被伪造
- 信息没有被篡改过，改过则哈希会变
- 继而说明签名者不可抵赖

![20231027212344](https://image.zuoright.com/20231027212344.png)

> 示例参考：<https://andersbrownworth.com/blockchain/public-private-keys/transaction>

- RSA
- DSA 电子签名算法
- ECDSA(Elliptic Curve Digital Signature Algorithm) 椭圆曲线数字签名算法，使用椭圆加密(ECC)的DSA变体，被比特币和以太坊，以及iOS大量使用

### 加密

将数据用公钥加密，发送给私钥所有者，数据无法被他人解密

但可能被他人篡改，因为每个人都可以用公钥加密数据，所以通常结合签名的方式一起使用防止篡改

- HTTPS传输

对称加密传输效率高，非对称更安全，所以HTTPS中将对称与非对称结合在一起使用

![加密](http://image.zuoright.com/加密.png)
