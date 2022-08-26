# Requests

[官方文档](https://requests.readthedocs.io/en/latest/){ .md-button .md-button--primary }

Requests是由著名开发者Kenneth Reitz基于urllib3（Python内置的HTTP库）封装的一个更加易用的HTTP库，它比ulrlib3更简易（[代码对比](https://gist.github.com/kennethreitz/973705)）

> Kenneth Reitz开发了很多人性化的工具，其个人网站：<https://kennethreitz.org/>
>
> 调试HTTP接口可以使用作者搭建的服务：<https://httpbin.org/>  
> 本地运行服务：`docker run -p 80:80 kennethreitz/httpbin`

`python -m pip install requests`

## requests.request

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

## requests.method

```python
import requests

payload = {'key1': 'value1', 'key2': 'value2'}
# 请求方式非常简单易懂
r = requests.get('https://httpbin.org/get', params=payload)  # 通常只用于获取数据，安全且幂等
r = requests.head('https://httpbin.org/get')  # 与get相同，但是不返回响应体，安全且幂等
r = requests.options('https://httpbin.org/get')  # 用于验证接口服务是否正常，安全且幂等

r = requests.post('https://httpbin.org/post', data=payload)  # 创建，不安全不幂等

r = requests.put('https://httpbin.org/put', data=payload)  # 通常用于更新，不安全但幂等
r = requests.patch('https://httpbin.org/patch', data=payload)  # 与put类似，通常用于部分更新，不安全但幂等

r = requests.delete('https://httpbin.org/delete')  # 删除，不安全但幂等

print(r)  # <Response [200]>
```

## requests.Response

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

## Cookies

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

## `requests.Session`

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
