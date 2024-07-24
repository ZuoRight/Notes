# Python 请求库

urllib3 是 Python 内置的 HTTP 请求库

Requests 是由著名开发者 Kenneth Reitz 基于 urllib3 封装的同步请求库，更加易用

AIOHTTP 是基于 ASyncio 实现的异步请求库，适合于纯异步请求的项目，性能更优，但据说有些坑？

HTTPX 是一个全功能的 HTTP 请求客户端，支持同步和异步请求，并且可支持 HTTP/2

除此之外，还有 http.client, urllib3, and httplib2 等请求库

- [Requests vs urllib3](https://gist.github.com/kennethreitz/973705)
- [HTTPX vs Requests vs AIOHTTP](https://oxylabs.io/blog/httpx-vs-requests-vs-aiohttp)

![20230813224801](https://image.zuoright.com/20230813224801.png)

## 内置

快速在当前路径启动一个服务：`python -m http.server 80`

比如电脑要访问 Android 手机的截图，可使用 [Termux](https://termux.dev/) 命令行工具启动一个服务

```shell
termux-setup-storage  # 开启访问权限
cd /sdcard/DCIM
python -m http.server 8080
```

电脑访问 `http://手机IP地址:8080` 即可查看

## Requests

> <https://requests.readthedocs.io/en/latest>

`python -m pip install requests`

### requests.request

<https://requests.readthedocs.io/en/latest/api/#requests.request>

```python
import requests

# 兼容各种请求方式
r = requests.request(method, url, **kwars)
"""
:method: GET/POST/PUT/DELETE/PATCH/OPTIONS/HEAD
:url 请求url
:**kwarts
    params=_dict  # url参数，即查询字符串
    data=_dict  # 表单
    json=_dict  # 表单，自动编码
    headers=_dict  # 请求头
    cookies=_dict  # Dict or CookieJar object
    files={"f1": open("xxx.xls", "rb"), "f2": open(...)}  # 文件
    proxies={"http": "127.0.0.1:8888", "https": "127.0.0.1:8888"}  # 设置代理
    verify="/path/to/certifile"
    timeout=0.01  # 设置连接超时时间，建议设为比3的倍数略大的一个数值，因为TCP数据包重传窗口的默认大小是3
    allow_redirects=True  # 自动重定向，默认True
"""
```

### requests.method

```python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
# 请求方式非常简单易懂
r = requests.get('https://httpbin.org/get', params=payload)
r = requests.head('https://httpbin.org/get')
r = requests.options('https://httpbin.org/get')

r = requests.post('https://httpbin.org/post', data=payload)

r = requests.put('https://httpbin.org/put', data=payload)
r = requests.patch('https://httpbin.org/patch', data=payload)

r = requests.delete('https://httpbin.org/delete')

print(r)  # <Response [200]>
```

### requests.Response

- 头信息

```python
# 查看路由（重定向）记录
r.history  # [<Response [301]>]

r.url  # 'https://httpbin.org/get?key1=value1&key2=value2'

r.encoding  # 'utf-8'

r.request.headers  # 请求头
r.headers  # 响应头
"""
{'Date': 'Wed, 24 Aug 2022 03:50:40 GMT', 'Content-Type': 'application/json', 'Content-Length': '379', 'Connection': 'keep-alive', 'Server': 'gunicorn/19.9.0', 'Access-Control-Allow-Origin': '*', 'Access-Control-Allow-Credentials': 'true'}

# 获取头字段值，不区分大小写
r.headers.get('content-type')  # 'application/json'
"""
```

- 响应码

<https://requests.readthedocs.io/en/latest/api/#api-cookies>

```python
r.status_code  # 200
# 使用内置状态码对象做判断
r.status_code == requests.codes.ok
# 检查请求是否成功
r.raise_for_status()
"""
成功返回：None
不成功则抛出异常：requests.exceptions.HTTPError
"""
```

- 响应内容

```python
# 字节码形式
r.content
"""
b'{\n  "args": {\n    "key1": "value1", \n    "key2": "value2"\n  }, \n  "headers": {\n    "Accept": "*/*", \n    "Accept-Encoding": "gzip, deflate", \n    "Host": "httpbin.org", \n    "User-Agent": "python-requests/2.28.1", \n    "X-Amzn-Trace-Id": "Root=1-6305a010-32d12aaa313962317e29ede5"\n  }, \n  "origin": "185.212.56.154", \n  "url": "https://httpbin.org/get?key1=value1&key2=value2"\n}\n'

# 图片的字节码可以直接用于构建图像
from PIL import Image
from io import BytesIO
i = Image.open(BytesIO(r.content))
"""

# 字符串形式
r.text
"""
'{\n  "args": {\n    "key1": "value1", \n    "key2": "value2"\n  }, \n  "headers": {\n    "Accept": "*/*", \n    "Accept-Encoding": "gzip, deflate", \n    "Host": "httpbin.org", \n    "User-Agent": "python-requests/2.28.1", \n    "X-Amzn-Trace-Id": "Root=1-6305a010-32d12aaa313962317e29ede5"\n  }, \n  "origin": "185.212.56.154", \n  "url": "https://httpbin.org/get?key1=value1&key2=value2"\n}\n'
"""

# json格式
r.json()
"""
# get
# params=payload
{'args': {'key1': 'value1', 'key2': 'value2'}, 'headers': {'Accept': '*/*', 'Accept-Encoding': 'gzip, deflate', 'Host': 'httpbin.org', 'User-Agent': 'python-requests/2.28.1', 'X-Amzn-Trace-Id': 'Root=1-6305a010-32d12aaa313962317e29ede5'}, 'origin': '185.212.56.154', 'url': 'https://httpbin.org/get?key1=value1&key2=value2'}

# post
# data=payload
{'args': {}, 'data': '', 'files': {}, 'form': {'k1': 'v1', 'k2': 'v2'}, 'headers': {'Accept': '*/*', 'Accept-Encoding': 'gzip, deflate', 'Content-Length': '11', 'Content-Type': 'application/x-www-form-urlencoded', 'Host': 'httpbin.org', 'User-Agent': 'python-requests/2.28.1', 'X-Amzn-Trace-Id': 'Root=1-63086975-1228aeea3fa968ba500d1c9d'}, 'json': None, 'origin': '123.118.73.22', 'url': 'https://httpbin.org/post'}

# post
# json=payload
{'args': {}, 'data': '{"k1": "v1", "k2": "v2", "k3": null}', 'files': {}, 'form': {}, 'headers': {'Accept': '*/*', 'Accept-Encoding': 'gzip, deflate', 'Content-Length': '36', 'Content-Type': 'application/json', 'Host': 'httpbin.org', 'User-Agent': 'python-requests/2.28.1', 'X-Amzn-Trace-Id': 'Root=1-630868d2-3c641ceb41c7147f5a160c5f'}, 'json': {'k1': 'v1', 'k2': 'v2', 'k3': None}, 'origin': '123.118.73.22', 'url': 'https://httpbin.org/post'}
"""

# 获取原始响应内容，请求时需要设置stream=True
r.raw
"""
<urllib3.response.HTTPResponse object at 0x105cd6dc0>
"""
r.raw.read(10)  # b'{\n  "args"'
# 通常保存到文件
with open(filename, 'wb') as fd:
    for chunk in r.iter_content(chunk_size=128):
        fd.write(chunk)
```

- 证书

```python
verify = True  # 校验证书，默认
verify = False  # 忽略证书验证，但会输出警告提示
"""
# 发送请求前加上这行代码，可关闭警告提示
requests.packages.urllib3.disable_warnings()
"""
verify="/path/to/certifile"  # 传入证书
cert=('/path/client.cert', '/path/client.key')  # 传入客户端证书
```

### Cookies

- Jar格式cookie

```python
# 从返回中获取
cookies_jar = res.cookies  # <RequestsCookieJar[]>

# 手动拼凑
cookies_jar = requests.cookies.RequestsCookieJar()
cookies_jar.set('tasty_cookie', 'yum', domain='httpbin.org', path='/cookies')
cookies_jar.set('gross_cookie', 'blech', domain='httpbin.org', path='/elsewhere')

# dict转jar
cookies_jar = requests.cookies.RequestsCookieJar()
for k,v in cookies_dict.items():
    cookies_jar.set(k, v)
```

- Dict格式cookie

```python
cookies_dict = {k1:v1, k2:v2}
cookies_dict = dict(k1="v1",k2="v2")

# jar转dict，方便保存
cookies_dict = requests.utils.dict_from_cookiejar(cookies_jar)
```

### `requests.Session`

```python
import requests

# Session对象拥有requests的一切方法
with requests.Session() as s:
    # 请求login接口，使用账号密码登陆，之后的session请求将默认携带cookies
    res = s.post('https://xxx.com/login', data={"username":xxx, "password":xxx})

    # 如果不请求login接口，而是直接访问需要登录的接口，可以手动添加cookies到session中
    """方式1"""
    s.cookies.update(cookies_dict/jar) # Updates this jar with cookies from another CookieJar or dict-like
    """方式2"""
    s.cookies = requests.cookies.cookiejar_from_dict(cookies_dict, cookiejar=None, overwrite=True)
    """
    # 方式2和1其实调用的都是requests.cookies.set_cookie(cookie)
    # 以下两种方式其实调用的都是方式2
    s.cookies = requests.utils.cookiejar_from_dict(cookies_dict, cookiejar=None, overwrite=True)
    requests.utils.add_dict_to_cookiejar(s.cookies, my_cookies_dict)
    """

    # 清空cookies
    s.cookies.clear()
```

## AIOHTTP

> <https://docs.aiohttp.org/en/stable/>

`pip install aiohttp`

```python
import aiohttp
import asyncio

url = "https://example.com"

async def main(): 
    async with aiohttp.ClientSession() as session: 
        async with session.get(url) as response:
        # async with session.post(url, data={"key": "value"}) as response:
            print(await response.text())

asyncio.run(main())
```

## HTTPX

> <https://www.python-httpx.org/>

```shell
pip install httpx
pip install httpx[http2]  # 支持HTTP2的组件

# 命令行客户端，类似curl
pip install 'httpx[cli]'
httpx --help
```

### 同步

与 requests 几乎相同

```python
import httpx

r1 = httpx.get("https://www.example.org")
r2 = httpx.post("https://httpbin.org/post", data={"name": "7c", "age": 30})

r  # <Response [200 OK]>
r.status_code  # 200
r.headers['content-type']  # 'text/html; charset=UTF-8'
r.text  # '<!doctype html>\n<html>\n<head>\n<title>Example Domain</title>...'
r.json()  # 自动解码
```

- HTTP2

```python
client = httpx.Client(http2=True)
r = client.get()
```

### 异步

```python
import httpx
import asyncio

async def main():
    async with httpx.AsyncClient() as client:
        response = await client.get("https://example.com")
        print(response.text)

asyncio.run(main())
```
