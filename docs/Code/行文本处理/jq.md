# JQ

一个专门用于处理和操作 JSON 数据的轻量级且强大的命令行工具，允许对 JSON 数据进行过滤、转换和查询等。

## 安装

- Ubuntu

```shell
sudo apt-get update
sudo apt-get install jq
```

- Mac

```shell
brew install jq
```

## 使用

```shell
# 格式化
curl http://httpbin.org/get | jq

# 格式化
cat data.json | jq .

`
{
  "name": "Alice",
  "age": 30,
  "city": "New York",
  "languages": ["English", "Spanish"],
  "address": {
    "street": "123 Main St",
    "zip": "10001"
  }
}
`

# 提取
cat data.json | jq '.name'  # "Alice"
cat data.json | jq '.address.street'  # "123 Main St"

# 过滤
cat data.json | jq 'select(.age > 25)'

# 修改
cat data.json | jq '.age = 35'

# 生成新的 json
cat data.json | jq '{name: .name, city: .city}'
```
