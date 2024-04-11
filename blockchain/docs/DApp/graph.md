# The Graph

<https://thegraph.com>

Graph 是一个去中心化的协议，用于索引和查询区块链的数据。它会统计某个合约从开始部署到现在所有的事件，Graph 节点不断扫描以太坊的新区块和它们可能包含的子图的数据，这些 API（索引的 "子图"）可以用标准的 GraphQL API 进行查询。

每次查询的平均成本约 0.0002 美元，以 `GRT` 代币支付。

![20240410190706](https://image.zuoright.com/20240410190706.png)

```shell
npm install -g @graphprotocol/graph-cli
graph --version

# 切换到项目根目录
mkdir subgraph && cd subgraph
graph init --product hosted-service github_name/graph_project_name
'
选择协议的类型：选ethereum；
填写subgraph名称：用默认的名称；
填写目录名：用默认目录名；
选择链：测试链名称；
填写合约地址：填入部署的地址0x8131...1ab0；
尝试自动获取ABI，一般都能成功；
填写从指定的块开始：查看合约部署的TX所在块填入区块高度；
填写合约名字：与合约代码保持一致；
是否索引事件：默认是。
'
graph auth  # 输入 Graph Access Token
npm run deploy
```
