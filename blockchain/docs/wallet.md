# HD钱包

> 参考：<https://wolovim.medium.com/ethereum-201-hd-wallets-11d0c93c87f7>

HD钱包指根据某种确定性算法，用一个根密钥（也叫根扩展密钥）即可衍生出 N 个子密钥,即可以是公钥也可以是私钥，理论上扩展密钥的层数是没有限制的，每一层的数量被限制在0～232

相当于管理一个扩展私钥（xprv），即可拥有 N 个子私钥，继而拥有多个钱包账户，通过一个扩展公钥（xpub）即可查询账户总余额。

> 在线生成工具：<https://iancoleman.io/bip39/>

- 私钥：64个十六进制字符（32bytes / 256bits）
- 公钥：128个十六进制字符（64bytes）
- 地址：42个十六进制字符（前缀0x+ 20bytes）
- 以太坊域名 ENS(Ethereum Name Service)，公钥可以绑定域名，方便记忆

![20220728154218](http://image.zuoright.com/20220728154218.png)

- BIP32 — 定义分层确定性钱包（Hierarchical Deterministic Wallets）
- BIP44 — 定义HD钱包多账户层次结构
- BIP39 — 使用助记词（Mnemonic code）生成确定性密钥

## 助记词 => seed

> 参考：<https://wolovim.medium.com/ethereum-201-mnemonics-bb01a9108c38>

直接记私钥很难记住，可以通过助记词来辅助记忆，根据助记词即可恢复钱包，所以助记词也叫SRP(Secret Recovery Phrase)，也可以直接备份私钥。

根据第BIP39，首先需要一个随机数（称之为熵），熵的位数需要是32的倍数，且介于128～256bits之间，即：`valid_entropy_bit_sizes = [128, 160, 192, 224, 256]`

```python
import os
from bitarray import bitarray

entropy_bit_size = 128  # 设置熵为128bits
entropy_bytes = os.urandom(entropy_bit_size // 8)  # 生成指定字节数的随机字节串，8bits/bytes，地板除取整
print(entropy_bytes)  # b'\xab9k\x1e\x00\xca\tdz\x1f\xb7\xd3\x8d\x06\xe7\xca'

# 将字节串转换为二进制bits形式
entropy_bits = bitarray()
entropy_bits.frombytes(entropy_bytes)
print(entropy_bits)  # bitarray('1011110001111...1111011111111001010') 共128bits
```

熵越大，助记词个数越多，每32bits增加三个助记词，依次递增为：`[12, 15, 18, 21, 24]`，即熵如果是128bits，需要对应12个助记词，但128并不能被12整除，此时需要在末尾增加checksum来补位，`checksum_length = entropy_bit_size // 32`，128bits就需要补4bits凑成132bits

取sha256(entropy_bytes)摘要二进制的前4位作为checksum补到entropy_bits后面

```python
import hashlib

hash_bytes = hashlib.sha256(entropy_bytes).digest()
print(hash_bytes)  # b'\xacK#\x93I$\x05b\x0b6\x83\xb2\x0b"\x9aE\x9a\x92\xed\x98YA\xbe\xe9%\xa0\xd7\xa3\x9aDs\xb2'

hash_bits = bitarray()
hash_bits.frombytes(hash_bytes) 
print(hash_bits)  # bitarray('1010110001001011001...01000111010') 共256bits

checksum_length = entropy_bit_size // 32  # 4
checksum = hash_bits[:checksum_length]  # 1010
entropy_bits.extend(checksum)
```

然后分成12组，每组助记词11bits（`132 // 12`）

```python
grouped_bits = tuple(entropy_bits[i * 11: (i + 1) * 11] for i in range(len(entropy_bits) // 11))
print(grouped_bits)
```

然后将二进制助记词转换为十进制整数，整数范围为0～2047（2^11），每个整数对应一个单词，共2048个，组成一个助记词列表

> 不同国家语言可以对应不同的助记词列表：<https://github.com/bitcoin/bips/blob/master/bip-0039/bip-0039-wordlists.md>

```python
from bitarray.util import ba2int

indices = tuple(ba2int(ba) for ba in grouped_bits)
print(indices)

# 假设使用English助记词列表
english_word_list = ['abandon', 'ability', ..., 'zone', 'zoo']
mnemonic_words = tuple(english_word_list[i] for i in indices)
print(mnemonic_words)  # ('face', 'business', 'large', 'tissue', 'print', 'box', 'fix', 'maple', 'arena', 'help', 'critic', 'border')
```

然后将助记词（mnemonic）转换为种子（seed）

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

## seed => root key

BIP32规范指出

1. 所有账户都源自一个根密钥（分层）
2. 给定根密钥，所有子账户都可以可靠地重新计算（确定性）

根据BIP44，层级结构为：`m / purpose’ / coin_type’ / account’ / change / address_index`

> `m` 表示根扩展密钥，`'` 表示硬化衍生，防止通过子私钥反推出父私钥

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

> Base58是专为Bitcoin设计的，相比于Base64,去除了`0, O, I, l`等容易被误认的字符

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

## root key => 子私钥

这个有些复杂，略过...

## 私钥 => 公钥

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
