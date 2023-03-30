# Ganache

- UI文档：<https://trufflesuite.com/docs/ganache/>
- RPC交互式文档：<https://ganache.dev/>
- CLI文档：<https://github.com/trufflesuite/ganache#readme>

Ganache 是 Truffle Suite 之一

- 安装

如果只使用UI版本可直接下载安装包，命令行版可使用NPM来安装

```shell
npm install ganache -g  # ganache-cli已被弃用，最新为ganache
ganache --version  # ganache v7.7.2 (@ganache/cli: 0.8.1, @ganache/core: 0.8.1)
ganache --help
```

- 运行

```shell
# 如果运行了UI需要关闭，否则可能会冲突
# 如果每次运行想保持生成的测试账户地址和私钥不变，可以加参数：-d（daterministic 确定性的）
ganache [-d]

# 每一次调用是一个json rpc调用区块链来进行交互
# Ganache控制台日志
<<'COMMENT'
eth_getTransactionCount
eth_gasPrice
eth_chainId
eth_estimateGas
eth_sendRawTransaction

  Transaction: 0xb9a6121bfde71828cd8c7256406a0d8c803ac7f8c71951588b3fb60607028946
  Contract created: 0xe78a0f7e598cc8b0bb87894b0f60dd2a88d6a8ab
  Gas usage: 471224
  Block number: 1
  Block time: Sat Dec 31 2022 01:45:57 GMT+0800 (中国标准时间)

eth_getTransactionReceipt
eth_chainId
eth_call
COMMENT
```
