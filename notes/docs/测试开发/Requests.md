# Requests

基于urllib3封装的Python HTTP库

使用文档：<https://docs.python-requests.org/zh_CN/latest/>

API：<https://docs.python-requests.org/zh_CN/latest/api.html>

`pip install requests`

```python
import requests


r = requests.request(request_method, request_url, **kwwars)  # <Response [200]>
"""
:request_method: get/post/put/delete/patch/options/head
:request_url 请求url
:**kwarts
    params = _dict  # url参数，即查询字符串
    data = _dict  # 表单
    json = _dict  # 表单，自动编码
    headers = _dict  # 请求头
    files = {"f1": open("xxx.xls", "rb"), "f2": open(...)}  # 文件
    proxies = {"http": "127.0.0.1:8888", "https": "127.0.0.1:8888"}  # 设置代理
    verify = "/path/to/certifile"  # True（校验证书，默认），False（忽略证书），字符串（从本地传入证书）
    timeout = 0.01  # 设置连接超时时间
    allow_redirects = True  # 自动重定向，默认True
"""

r.url
r.encoding
r.headers  # 获得响应头

r.content  # 获取响应内容，字节码形式
r.text  # 获取响应内容，字符串形式
r.json  # 获取响应内容，json格式
r.raw  # 获取原始响应内容，请求时需要加stream=True，并建议保存到文件，参考文档：https://docs.python-requests.org/zh_CN/latest/user/quickstart.html#id5


r.raise_for_status()
r.status_code
```

## 获取cookies

- dict格式：从浏览器返回中获取

```python
cookies_dict = {k1:v1, k2:v2}
```

- RequestsCookieJar格式：从requests请求返回中获取

```python
cookies_jar = res.cookies
```

## cookies格式转换

- dict 转 RequestsCookieJar

```python
cookies_jar = requests.cookies.RequestsCookieJar()
for k,v in cookies_dict.items():
    cookies_jar.set(k, v)
```

- RequestsCookieJar 转 dict

```python
cookies_dict = requests.utils.dict_from_cookiejar(cookies_jar)
```

## session中保持cookies

`s = requests.Session()`

```python
s.cookies.update(my_cookies_dict)
```

```python
requests.utils.add_dict_to_cookiejar(s.cookies, my_cookies_dict)
```

```python
s.cookies = requests.utils.cookiejar_from_dict(my_cookies_dict)
```
