# 引言

数字和字符都可以通过编码直接转为二进制，而结构体则需要序列化

- 序列化（Serialization）就是将数据结构或对象转换为一种标准化的格式，比如 JSON、YAML、XML、二进制等，以便于存储或传输，通常涉及写操作。
- 反序列化（Deserialization）将存储或传输的格式恢复成原始数据结构或对象的过程，通常涉及读操作。

![20240607200718](https://image.zuoright.com/20240607200718.png)

以 Json 反序列化为例，从文件对象反序列化，使用 `load()`，从字符串对象反序列化使用 `loads()`
