# HD Wallet

- BIP32 — 定义分层确定性钱包（Hierarchical Deterministic Wallets）
- BIP44 — 定义HD钱包多账户层次结构
- BIP39 — 使用助记词（Mnemonic）来生成确定性密钥，根据助记词即可恢复钱包，也SRP(Secret Recovery Phrase)

在 BIP32 推出之前，用户需要记录一堆的私钥才能管理很多账户，BIP32 提出可以用一个根密钥（也叫根扩展密钥）根据某种确定性算法派生出多个子密钥，更方便的管理多个账户，即

1. 所有账户都源自一个根密钥（分层）
2. 给定根密钥，所有子账户都可以可靠地重新计算（确定性）

相当于管理一个扩展私钥（xprv），即可拥有 N 个子私钥，继而拥有多个钱包账户，通过一个扩展公钥（xpub）即可查询账户总余额

## 在线生成工具

- <https://iancoleman.io/bip39/>
- <https://bip39.onekey.so/>

![20231026000509](https://image.zuoright.com/20231026000509.png)

![20231026000641](https://image.zuoright.com/20231026000641.png)

## 熵 entropy

根据第BIP39，首先需要一个随机数，也称为熵

熵的随机性一定要高，否则就可以被暴力破解，通常要求位数介于 128～256bits 之间，且需要是 32 的倍数，即：`128, 160, 192, 224, 256`

每 32bits 增加三个助记词，所以熵越大，对应的助记词个数越多，即：`12, 15, 18, 21, 24`

比如常用的 128bits，有 2^128 种可能性，对应有 12 个助记词

```python
import os
from bitarray import bitarray

# 设置熵为128bits，8bits=1Bytes，128bits=16Bytes
entropy_bit_size = 128

# 生成一个不可预测的(unpredictable)随机字节串
entropy_bytes = os.urandom(entropy_bit_size // 8)
# b'\xab9k\x1e\x00\xca\tdz\x1f\xb7\xd3\x8d\x06\xe7\xca'
# len(entropy_bytes) == 16

# 将随机16字节串转为128位二进制格式的熵
entropy_bits = bitarray()
entropy_bits.frombytes(entropy_bytes)
# bitarray('101111000...011111111001010')
```

熵如果是128bits，需要对应12个助记词，但128并不能被12整除，此时需要在末尾增加校验和 `checksum` 来补位

校验和的长度取决于熵的大小：`checksum_length = entropy_bit_size // 32`，即 4 位

然后取 sha256(entropy_bytes) 摘要的二进制结果的前 4 位作为补到 entropy_bits 后

```python
import hashlib

hash_bytes = hashlib.sha256(entropy_bytes).digest()  # 256bits
# b'\xacK#\x93I$\x05b\x0b6\...\xbe\xe9%\xa0\xd7\xa3\x9aDs\xb2'

# 转成二进制
hash_bits = bitarray()
hash_bits.frombytes(hash_bytes)
# bitarray('1010110001001011001...01000111010')

# 需要补4bits凑成可被12整除的132bits
checksum_length = entropy_bit_size // 32
checksum = hash_bits[:checksum_length]  # 取前4位，1010
entropy_bits.extend(checksum)
```

## 助记词 mnemonic

标准的助记词列表：<https://github.com/bitcoin/bips/blob/master/bip-0039/bip-0039-wordlists.md>

有不同语言的版本，但通常使用英文，每种语言都包含 2048 个助记词，每个助记词对应一个索引

> 虽然可以自定义助记词列表，但由于不符合 BIP39，可能无法导入常见的钱包中

### entropy => mnemonic

将 132bits 的 `熵 + 校验和` 平分成12组，每组 11bits (132 // 12) ，被称之为 magic number，然后将二进制的 magic number 转换为十进制整数，即得到12个助记词的索引

```python
from bitarray.util import ba2int

# magic number list
grouped_bits = tuple(entropy_bits[i * 11: (i + 1) * 11] for i in range(12)

# 助记词索引列表
indices = tuple(ba2int(ba) for ba in grouped_bits)

# 假设使用English助记词列表
english_word_list = ['abandon', 'ability', ..., 'zone', 'zoo']
mnemonic_words = tuple(english_word_list[i] for i in indices)
# ('face', 'business', 'large', 'tissue', 'print', 'box', 'fix', 'maple', 'arena', 'help', 'critic', 'border')
```

### mnemonic => seed

助记词只有转化为种子才可以派生出私钥和公钥

种子是根据助记词使用 `PBKDF2`（Password-Based Key Derivation Function）算法生成的 512bits 数，通常表示为 64Bytes 的 16 进制形式

```python
import hashlib

passphrase = "you-make-this-up"  # 自定义密码，可默认为空
salt = "mnemonic" + passphrase  # 盐，进一步提高钱包的安全性

# 将12个助记词拼接为以空格分隔的字符串形式
mnemonic_string = ' '.join(mnemonic_words)  # 'across abstract shine ... uphold already club'

seed = hashlib.pbkdf2_hmac(
  "sha512",  # 伪随机函数：HMAC-SHA512
  mnemonic_string.encode("utf-8"),  # 助记词
  salt.encode("utf-8"),  # salt
  2048  # 哈希函数将运行的迭代次数
)
print(seed)  # b'\xcd@\xd0}\xbc\x17\xd6H\x00\x1c\xdc...'
print(seed.hex())  # cd40d07dbc17d648001cdc84473be584...
```

## 根密钥

### seed => root key

```python
import binascii
import hmac
import hashlib

# the HMAC-SHA512 `key` and `data` must be Bytes:
seed_bytes = binascii.unhexlify(seed)
# I = HMAC-SHA512(Key = “Bitcoin seed”, Data = seed)
I = hmac.new(b'Bitcoin seed', seed_bytes, hashlib.sha512).digest()
# Split I into two 32-byte sequences, L and R.
L, R = I[:32], I[32:]
# Use parse256(L) as master secret key, and R as master chain code.
master_private_key = int.from_bytes(L, 'big')
master_chain_code = R  # 用作熵
```

root key 通常表示为扩展私钥（xprv开头），根据BIP32，extended private keys are a Base58 encoding of the private key, chain code, and some additional metadata.

> Base58是专为Bitcoin设计的，相比于Base64，去除了`0, O, I, l`等容易被误认的字符

```python
import base58

VERSION_BYTES = {
  'mainnet_public': binascii.unhexlify('0488b21e'),
  'mainnet_private': binascii.unhexlify('0488ade4'),
  'testnet_public': binascii.unhexlify('043587cf'),
  'testnet_private': binascii.unhexlify('04358394'),
}
version_bytes = VERSION_BYTES['mainnet_private']
depth_byte = b'\x00'
parent_fingerprint = b'\x00' * 4
child_number_bytes = b'\x00' * 4
key_bytes = b'\x00' + L
all_parts = (
  version_bytes,      # 4 Bytes 版本字节
  depth_byte,         # 1 byte 深度
  parent_fingerprint,  # 4 Bytes 父密钥指纹
  child_number_bytes, # 4 Bytes 子编号
  master_chain_code,  # 32 Bytes 熵
  key_bytes,          # 33 Bytes 公钥或私钥数据
)
all_bytes = b''.join(all_parts)
root_key = base58.b58encode_check(all_bytes).decode('utf8')  # xprv9s21ZrQH143K...T2emdEXVYsCzC2U
```

### root_key => sub_private_key

根据派生路径生成子密钥

![20231025205912](https://image.zuoright.com/20231025205912.png)

扩展密钥每一层可派生的数量被限制在0～232个账户，但没有限制可派生的层数

BIP44 为 BIP32 的派生路径提供了一套通用规范，适配比特币、以太坊等

这一套规范包含六层，每层之间用斜线分割，比如以太坊的默认派生路径为：`m/44'/60'/0'/0/0`

```shell
# m 是固定的，表示根扩展密钥
# ' 表示该值已硬化，防止通过子私钥反推出父私钥
m / purpose’ / coin_type’ / account’ / change / address_index
# purpose’ 固定为 44，表示遵循 BIP44 标准
# coin_type 表示代币类型，比特币主网为0，测试网为1，以太坊主网为60
# account 表示不同类型的钱包用户，比如同一个公司不同部门，从0开始索引
# change 是否为外部链，主要用于比特币，以太坊通常为0
# address_index 账户索引，从0开始
```

Extended keys can be identified by the Hash160 (RIPEMD160 after SHA256) of the serialized ECDSA public key K, ignoring the chain code. […] The first 32 bits of the identifier are called the key fingerprint. […] Note that the fingerprint of the parent only serves as a fast way to detect parent and child nodes in software…

### private_key => public_key

```python
p = curve_point_from_int(private_key)
public_key_bytes = serialize_curve_point(p)
print(f'public key: 0x{public_key_bytes.hex()}')  # public key: 0x024c8f4044470bd42b81a...
```

### public_key => address

a public address is the last 20 Bytes of the Keccak-256 hash of the public key points

```python
from eth_utils import keccak

digest = keccak(x.to_bytes(32, 'big') + y.to_bytes(32, 'big'))
address = '0x' + digest[-20:].hex()
print(f'public address: {address}')  # public address: 0xbbec2620cb01adae3f96e1fa39f997f06bfb7ca0
```

## 使用 Ether.js 生成 HD

- 生成随机助记词

```js
// import { ethers } from 'ethers';
const ethers = require('ethers')

// 生成随机助记词索引，等价于熵
const entropy = ethers.randomBytes(32)
`
Uint8Array(32) [
  146, 149,  59,  53, 253,  65, 158,
  225, 172, 172,  75, 118, 178,  15,
  235, 114, 233,  26, 196, 105,  13,
  176, 130, 150, 239, 159, 211, 181,
  125, 246, 252, 243
]
`

// 转为助记词
const mnemonic = ethers.Mnemonic.entropyToPhrase(entropy)
'myth prefer sniff whisper boring ignore razor maximum issue motor width total museum giraffe picnic render little resist tree polar fit window video grab'
```

- 创建HD钱包

```js
const hdNode = ethers.HDNodeWallet.fromPhrase(mnemonic)
`
HDNodeWallet {
  provider: null,
  address: '0xE46A4BD6908b05d60E49cb4E52b2425C4d9742B3',
  publicKey: '0x02bedf3b217145b9ac7d695adead9bfbe13475b7f25c8f4c7295d6addd3aff1e36',
  fingerprint: '0x57e4f425',
  parentFingerprint: '0xbddc8a6c',
  mnemonic: Mnemonic {
    phrase: 'myth prefer sniff whisper boring ignore razor maximum issue motor width total museum giraffe picnic render little resist tree polar fit window video grab',
    password: '',
    wordlist: LangEn { locale: 'en' },
    entropy: '0x92953b35fd419ee1acac4b76b20feb72e91ac4690db08296ef9fd3b57df6fcf3'
  },
  chainCode: '0xd2148e93be5aa3690e17d7f71614d33064a663eded00d5f4513b34ddec16e6ef',
  path: "m/44'/60'/0'/0/0",
  index: 0,
  depth: 5
}
`

// 方式2
const hdNode = ethers.Wallet.fromPhrase(mnemonic)

// 方式3，直接生成随机钱包
ethers.Wallet.createRandom()
`
HDNodeWallet {
  provider: null,
  address: '0x69B619CdedB9b7C46efDed3d2FA0C7d9083D0fd8',
  publicKey: '0x02d474c5d7fa9dedc3707203245368a11f541fb1efe3983eabb3dd6d1a95af7256',
  fingerprint: '0x013da586',
  parentFingerprint: '0x0e24401f',
  mnemonic: Mnemonic {
    phrase: 'quick large faculty bitter bunker produce identify shine toe staff oil faith',
    password: '',
    wordlist: LangEn { locale: 'en' },
    entropy: '0xafafa5468b61e7575c262fe39a826729'
  },
  chainCode: '0x92f9e2c958658f67d19ea0a82ed4f1d7fc8a16b0fefb40eb7716a4351ae101f7',
  path: "m/44'/60'/0'/0/0",
  index: 0,
  depth: 5
}
`
```

- 生成靓号地址

```js
// 靓号地址不仅好看，而且可以节省 Gas Fee
import { ethers } from "ethers";

var wallet  // 钱包
const regex = /^0x000.*$/  // 正则匹配前3位都是0的地址

var isValid = false
while(!isValid){
  wallet = ethers.Wallet.createRandom()  // 随机生成钱包
  isValid = regex.test(wallet.address)  // 选出符合条件的地址后停止循环
}

console.log(wallet)
console.log(`靓号地址：${wallet.address}`)
console.log(`靓号私钥：${wallet.privateKey}`)
```

- 派生20个钱包地址

```js
// 只需要切换派生路径最后一层的 address_index，就可以从 hdNode 派生出新钱包
let basePath = "m/44'/60'/0'/0";
let wallets = [];
for (let i = 0; i < 20; i++) {
  let hdNodeNew = hdNode.derivePath(basePath + "/" + i);
  let walletNew = new ethers.Wallet(hdNodeNew.privateKey);
  console.log(`第 ${i+1} 个钱包地址： ${walletNew.address}`)
  wallets.push(walletNew);
}
`
第 1 个钱包地址： 0x9F17034925c638D0b0C886c0ad1863ff330e35c6
第 2 个钱包地址： 0xB0AD00eB4473291ca82c27B79b72e3045C01A4C5
第 3 个钱包地址： 0xb75B8f716617bf67b2eDE9AAa4801e09A75ABDC5
第 4 个钱包地址： 0x195D66FAE77e9e2B0A455Fc55b7ADB91D609bAB6
第 5 个钱包地址： 0xC3659A3A7DC8620a7c8E8511DB85D47D1c6296B8
第 6 个钱包地址： 0x7b18DCc37350FF04e6a2CF433eC9964AeCcD49fF
第 7 个钱包地址： 0xDAb049bf753A3034E7b3C33a30E88aEd2A290693
第 8 个钱包地址： 0xC97e301B5cbFC41B19d83675fb54EC5b04bee024
第 9 个钱包地址： 0x244C436E2f90Fa58CC16206F691cBdc2C38993E2
第 10 个钱包地址： 0x96531338Bf1F324c9Ff0F045c8a7b41763963c7D
第 11 个钱包地址： 0xaDE981d55A8416BD4d54C00C635918C5A21c10d9
第 12 个钱包地址： 0x99cfba6992f59DC2311B841eb043Fd3a406D3E9f
第 13 个钱包地址： 0xdEAE53329b05894B1ed75b9b2aE6FE1CE15f7CAB
第 14 个钱包地址： 0xd77d6EEbd6F3ADf40D626D71f054027738900F50
第 15 个钱包地址： 0x2cB9D101788578691C3249344fBb5b769259Bd11
第 16 个钱包地址： 0x90cc35330297E1eDA46AaA01A0F1a09D8a1978C4
第 17 个钱包地址： 0x099320507920A2257c894A42Dd3f379B33969893
第 18 个钱包地址： 0x133326eC80078bEC7aEA6f77c6F78f4049c6a75a
第 19 个钱包地址： 0xd4A19Bff496B7b0100A8d5f2A8D9f38302Bd0C1a
第 20 个钱包地址： 0x1a93BA5C762B51f58538f81a46558352781c0564
`
```

- 将钱包保存为加密Json数据

```js
const pwd = "password"  // 设置加密Json用的密码
const json = await hdNode.encrypt(pwd)
`{"address":"e46a4bd6908b05d60e49cb4e52b2425c4d9742b3","id":"775c9c19-c95c-48bb-8fc0-b13f70e0034a","version":3,"Crypto":{"cipher":"aes-128-ctr","cipherparams":{"iv":"a78beec4021ca1be6e6f0147b25d9f4a"},"ciphertext":"a4efc0ca71596530722f97ffcab7c08883b822b4aec4fc350e79e21b012bbefc","kdf":"scrypt","kdfparams":{"salt":"573fcd06d419c5122f54678bacf9055eb7c9edeb431a70e6d2780b0187afa662","n":131072,"dklen":32,"p":1,"r":8},"mac":"a562d79e62ea3329b7ad41caaf301e93ff022e68883e5fa13ae6beed16457544"},"x-ethers":{"client":"ethers/6.7.0","gethFilename":"UTC--2023-10-25T14-43-29.0Z--e46a4bd6908b05d60e49cb4e52b2425c4d9742b3","path":"m/44'/60'/0'/0/0","locale":"en","mnemonicCounter":"bc4e276f1b2958826bb7f53e21629e12","mnemonicCiphertext":"ce9524a3fbccd4ad2a6597c094d418ceaba6e94ad4badcca4ea619f7208551c8","version":"0.1"}}`
```

- 从加密Json中读取钱包

```js
const wallet = await ethers.Wallet.fromEncryptedJson(json, pwd);
```
