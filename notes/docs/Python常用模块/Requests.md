# Requests

基于urllib3封装的Python HTTP库

[使用文档](https://docs.python-requests.org/zh_CN/latest/){ .md-button .md-button--primary }

[API](https://docs.python-requests.org/zh_CN/latest/api.html){ .md-button .md-button--primary }

`pip install requests`

```python
import requests


r = requests.request(request_method, request_url, **kwars)  # <Response [200]>
"""
:request_method: get/post/put/delete/patch/options/head
:request_url 请求url
:**kwarts
    params=_dict  # url参数，即查询字符串
    data=_dict  # 表单
    json=_dict  # 表单，自动编码
    headers=_dict  # 请求头
    cookies=_dict  # Dict or CookieJar object
    files={"f1": open("xxx.xls", "rb"), "f2": open(...)}  # 文件
    proxies={"http": "127.0.0.1:8888", "https": "127.0.0.1:8888"}  # 设置代理
    verify="/path/to/certifile"  # True（校验证书，默认），False（忽略证书），字符串（从本地传入证书）
    timeout=0.01  # 设置连接超时时间，建议设为比3的倍数略大的一个数值，因为TCP数据包重传窗口的默认大小是3
    allow_redirects=True  # 自动重定向，默认True
"""

r.url
r.encoding
r.headers  # 获得响应头

r.content  # 获取响应内容，字节码形式
r.text  # 获取响应内容，字符串形式
r.raw  # 获取原始响应内容，请求时需要加stream=True，并建议保存到文件，参考文档：https://docs.python-requests.org/zh_CN/latest/user/quickstart.html#id5
r.json()  # 获取响应内容，json格式
r.raise_for_status()  # 检查请求是否成功，成功返回None，不成功则抛出异常：requests.exceptions.HTTPError
r.status_code  # 状态码
```

## 证书

```python
verify = False  # 忽略证书验证，但会输出警告提示
requests.packages.urllib3.disable_warnings()  # 发送请求前加上这行代码，可关闭警告提示
```

## 获取cookies

- RequestsCookieJar格式

```python
# 从requests请求返回中自动获取
cookies_jar = res.cookies
```

- dict格式

```python
# 从浏览器返回中手动获取
cookies_dict = {k1:v1, k2:v2}
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
cookies_jar = requests.cookies.cookiejar_from_dict(my_cookies_dict)
```

## session中保持cookies

`s = requests.Session()`

```python
s.cookies.update(my_cookies_dict)

s.cookies = requests.cookies.cookiejar_from_dict(my_cookies_dict)

requests.utils.add_dict_to_cookiejar(s.cookies, my_cookies_dict)
```
