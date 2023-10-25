# HD钱包

- BIP32 — 定义分层确定性钱包（Hierarchical Deterministic Wallets）
- BIP44 — 定义HD钱包多账户层次结构
- BIP39 — 使用助记词（Mnemonic code）生成确定性密钥方便记忆，根据助记词即可恢复钱包，所以助记词也叫SRP(Secret Recovery Phrase)

在 BIP32 推出之前，用户需要记录一堆的私钥才能管理很多钱包。

BIP32 提出可以用一个随机种子衍生多个私钥，更方便的管理多个钱包

BIP32 规范指出：

1. 所有账户都源自一个根密钥（分层）
2. 给定根密钥，所有子账户都可以可靠地重新计算（确定性）

![20231025205912](https://image.zuoright.com/20231025205912.png)

HD 钱包指根据某种确定性算法，用一个根密钥（也叫根扩展密钥）即可衍生出 N 个子密钥，即可以是公钥也可以是私钥，理论上扩展密钥的层数是没有限制的，每一层的数量被限制在0～232

相当于管理一个扩展私钥（xprv），即可拥有 N 个子私钥，继而拥有多个钱包账户，通过一个扩展公钥（xpub）即可查询账户总余额

BIP44 为 BIP32 的衍生路径提供了一套通用规范，适配比特币、以太坊等

这一套规范包含六层，每层之间用斜线分割

```shell
m / purpose’ / coin_type’ / account’ / change / address_index

# m 表示根扩展密钥
# ' 表示硬化衍生，防止通过子私钥反推出父私钥
# purpose’ 固定为 44
# coin_type：代币类型，比特币主网为0，比特币测试网为1，以太坊主网为60
# account：账户索引，从0开始
# change：是否为外部链，0为外部链，1为内部链，一般填0
# address_index：地址索引，从0开始，想生成新地址就把这里改为1，2，3
```

![20220728154218](http://image.zuoright.com/20220728154218.png)

## 在线生成

> 工具：<https://iancoleman.io/bip39/>

比如，以太坊的默认衍生路径为：`m/44'/60'/0'/0`

![20231026000509](https://image.zuoright.com/20231026000509.png)

![20231026000641](https://image.zuoright.com/20231026000641.png)

- 助记词

根据第BIP39，首先需要一个随机数，称之为熵，熵的位数需要是 32 的倍数，熵越大，助记词个数越多

不同语言可以对应不同的助记词列表：<https://github.com/bitcoin/bips/blob/master/bip-0039/bip-0039-wordlists.md>

通用的助记词列表一般包含 2048 个助记词，每个助记词对应一个索引

- 私钥：64个十六进制字符（32bytes / 256bits）
- 公钥：128个十六进制字符（64bytes）
- 地址：42个十六进制字符（前缀0x+ 20bytes）

## 使用 Ether.js 生成

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

## 使用 Python 生成

### 随机数 => mnemonic

```python
import os
from bitarray import bitarray

# 设置熵为128bits
entropy_bit_size = 128

# 生成随机字节串，1Bytes=8bits，地板除取整，128bits=12字节
entropy_bytes = os.urandom(entropy_bit_size // 8)  # b'\xab9k\x1e\x00\xca\tdz\x1f\xb7\xd3\x8d\x06\xe7\xca'

# 将字节串转换为二进制格式
entropy_bits = bitarray()
entropy_bits.frombytes(entropy_bytes)  # bitarray('101111000...011111111001010')
```

熵如果是128bits，需要对应12个助记词，但128并不能被12整除，此时需要在末尾增加checksum来补位

需要补的位数：`checksum_length = entropy_bit_size // 32`

取 sha256(entropy_bytes) 摘要的二进制的前 checksum_length 位作为 checksum 补到 entropy_bits 后面

```python
import hashlib

hash_bytes = hashlib.sha256(entropy_bytes).digest()  # b'\xacK#\x93I$\x05b\x0b6\...\xbe\xe9%\xa0\xd7\xa3\x9aDs\xb2'

hash_bits = bitarray()
hash_bits.frombytes(hash_bytes)  # bitarray('1010110001001011001...01000111010') 共256bits

checksum_length = entropy_bit_size // 32  # 128bits 需要补 128/32=4bits 凑成 132bits
checksum = hash_bits[:checksum_length]  # 取前4位，1010
entropy_bits.extend(checksum)

# 然后分成12组，132 // 12 = 11，即每组助记词 11bits
grouped_bits = tuple(entropy_bits[i * 11: (i + 1) * 11] for i in range(len(entropy_bits) // 11))
```

然后将二进制助记词转换为十进制整数

```python
from bitarray.util import ba2int

indices = tuple(ba2int(ba) for ba in grouped_bits)

# 假设使用English助记词列表
english_word_list = ['abandon', 'ability', ..., 'zone', 'zoo']
mnemonic_words = tuple(english_word_list[i] for i in indices)
# ('face', 'business', 'large', 'tissue', 'print', 'box', 'fix', 'maple', 'arena', 'help', 'critic', 'border')
```

### mnemonic => seed

根据助记词生成种子

```python
import hashlib

mnemonic_string = ' '.join(mnemonic_words)  # 先将元组拼接为空格分隔的字符串形式：'across abstract shine ... uphold already club'
passphrase = "you-make-this-up"  # 自定义密码
salt = "mnemonic" + passphrase  # 提高钱包的安全性

# 使用PBKDF2（Password-Based Key Derivation Function）生成512bits的种子，通常表现为64bytes的16进制形式
seed = hashlib.pbkdf2_hmac(
   "sha512",  # 伪随机函数：HMAC-SHA512
   mnemonic_string.encode("utf-8"),  # 助记词
   salt.encode("utf-8"),  # salt
   2048  # 哈希函数迭代次数
)
print(seed)  # b'\xcd@\xd0}\xbc\x17\xd6H\x00\x1c\xdc...'
print(seed.hex())  # cd40d07dbc17d648001cdc84473be584...
```

### seed => root key

![20220728150444](http://image.zuoright.com/20220728150444.png)

```python
import binascii
import hmac
import hashlib

# the HMAC-SHA512 `key` and `data` must be bytes:
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
    version_bytes,      # 4 bytes 版本字节
    depth_byte,         # 1 byte 深度
    parent_fingerprint,  # 4 bytes 父密钥指纹
    child_number_bytes, # 4 bytes 子编号
    master_chain_code,  # 32 bytes 熵
    key_bytes,          # 33 bytes 公钥或私钥数据
)
all_bytes = b''.join(all_parts)
root_key = base58.b58encode_check(all_bytes).decode('utf8')  # xprv9s21ZrQH143K...T2emdEXVYsCzC2U
```

### root key => 子私钥

这个有些复杂，略过...

### 私钥 => 公钥

生成私钥后根据椭圆曲线密码学即可生成公钥

```python
p = curve_point_from_int(private_key)
public_key_bytes = serialize_curve_point(p)
print(f'public key: 0x{public_key_bytes.hex()}')  # public key: 0x024c8f4044470bd42b81a...
```

然后生成一个收款地址，a public address is the last 20 bytes of the Keccak-256 hash of the public key points（取64bytes的后20bytes）

```python
from eth_utils import keccak

digest = keccak(x.to_bytes(32, 'big') + y.to_bytes(32, 'big'))
address = '0x' + digest[-20:].hex()
print(f'public address: {address}')  # public address: 0xbbec2620cb01adae3f96e1fa39f997f06bfb7ca0
```

## ENS

<https://app.ens.domains/>

类似 DNS 的以太坊域名服务 ENS（Ethereum Name Service）

钱包地址类似于IP，可以绑定域名，方便记忆

比如V神的域名为：[vitalik.eth](https://app.ens.domains/vitalik.eth)

```js
// 将ENS解析为地址
const addressVitalik = await provider.resolveName("vitalik.eth")
```

## 参考

- <https://ethereum.org/en/wallets/find-wallet/#main-content>
- <https://wolovim.medium.com/ethereum-201-mnemonics-bb01a9108c38>
- <https://wolovim.medium.com/ethereum-201-hd-wallets-11d0c93c87f7>
