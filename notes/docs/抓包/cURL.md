# cURL

```shell
# 查看帮助
curl -h

url_get = http://httpbin.org/get
url_post = http://httpbin.org/post

# 默认请求方式为GET
curl $url_get
# 指定请求方式
curl -X POST $url_post
# 使用-d参数设置请求体，默认以POST方式发送请求，可以省略-X POST
# -d还可以从本地文件读取数据：-d "@data.json"
curl -d/--data "name=7c" -d "password=123" $url_post
# 加上-G，则会变为GET请求：http://httpbin.org/get?name=7c&password=123
curl -d "name=7c" -d "password=123" $url_get -G
# --data-urlencode参数等同于-d，但是会将发送数据进行URL编码
curl --data-urlencode "name=7 c" $url_get -G  # 7c之间有空格，需要url编码

# 指定代理（方便抓包）
curl -x/--proxy "http://localhost:8888" $url_post

# 跳过SSL检测
curl -k

# 自动跳转，因为默认不会重定向
curl -L/--location $url_get

# 设置User-Agent
curl -A

# 设置Authorization
curl -u 7c:123

# 设置请求头
curl -H

# 设置请求体
curl -X POST $url_post --data '{"a":111, "b":222}'
```

```shell
# 只输出响应头
curl -I/--head

# 输出更详细
curl -v/--verbose

# 输出保存到文件
curl -o/--output
```

另外输出结果可以借助jq来处理或者格式化

```shell
sudo apt-get update
sudo apt-get install jq

curl $url_get | jq
```
