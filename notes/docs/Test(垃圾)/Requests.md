# Requests

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
