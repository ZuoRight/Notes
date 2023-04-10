# Graph

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
`
