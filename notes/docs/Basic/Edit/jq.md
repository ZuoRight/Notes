# JQ

安装

```shell
sudo apt-get update
sudo apt-get install jq
```

```shell
jq . config.json  # 检查json格式
```

一些命令的输出结果可以借助jq来处理或者格式化

```shell
curl http://httpbin.org/get | jq
```
