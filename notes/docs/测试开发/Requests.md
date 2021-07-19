# Requests

`pip install requests`

```python
import requests

def req(request_method, request_url, **kwwars):
    """
    :request_method: get/post/put/delete
    :request_url 请求url
    :**kwarts
        headers = _dict  # 请求头
        data = {k1:v1, k2:v2}  # 表单
        files = {"f1": open("xxx.xls", "rb"), "f2": open(...)}  # 文件
        proxies = {"http": "127.0.0.1:8888", "https": "127.0.0.1:8888"}  # 设置代理
        verify = "/path/to/certifile"  # True（校验证书，默认），False（忽略证书），字符串（从本地传入证书）
        timeout = 0.01  # 设置响应超时时间
        allow_redirects = True  # 是否自动重定向，默认False
    """
    r = requests.request(request_method, request_url, **kwwars)
    return r

r = req()  # <Response [状态码]>
r.headers  # 获得响应头
r.raw  # 获得原始内容
r.content  # 获得二进制响应内容
r.text  # 获得编码后的响应值
r.json  # 获得json格式响应体
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
