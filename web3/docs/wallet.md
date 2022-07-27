# 钱包

## 助记词

从助记词生成种子，首先需要一个随机数（称之为熵），根据BIP(比特币提案)39规范，，熵的位数需要是32的倍数，且介于128～256位之间，即：`valid_entropy_bit_sizes = [128, 160, 192, 224, 256]`

```python
import os
from bitarray import bitarray

entropy_bit_size = 128  # 设置熵为128位
entropy_bytes = os.urandom(entropy_bit_size // 8)  # 生成指定字节数的随机字节串 8位一字节，地板除取整
print(entropy_bytes)  # b'\xab9k\x1e\x00\xca\tdz\x1f\xb7\xd3\x8d\x06\xe7\xca'

# 将字节串转换为二进制位形式
entropy_bits = bitarray()
entropy_bits.frombytes(entropy_bytes)
print(entropy_bits)  # bitarray('1011110001111...1111011111111001010') 共128位
```

熵越大，助记词个数越多，每32位增加三个助记词，依次递增为：`[12, 15, 18, 21, 24]`，即熵如果是128位，需要对应12个助记词，但128并不能被12整除，此时需要在末尾增加checksum来补位，`checksum_length = entropy_bit_size // 32`，128位就需要补4位凑成132位

取sha256(entropy_bytes)摘要二进制的前4位作为checksum补到entropy_bits后面

```python
from hashlib import sha256

hash_bytes = sha256(entropy_bytes).digest()
print(hash_bytes)  # b'\xacK#\x93I$\x05b\x0b6\x83\xb2\x0b"\x9aE\x9a\x92\xed\x98YA\xbe\xe9%\xa0\xd7\xa3\x9aDs\xb2'

hash_bits = bitarray()
hash_bits.frombytes(hash_bytes) 
print(hash_bits)  # bitarray('1010110001001011001...01000111010') 共256位

checksum_length = entropy_bit_size // 32  # 4
checksum = hash_bits[:checksum_length]
print(checksum)
entropy_bits.extend(checksum)
```

然后分成12组，每组助记词11位（`132 // 12`）

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
