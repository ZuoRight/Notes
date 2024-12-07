# 引言

API, Application Programming Interface

<https://apievangelist.com/info/101/>

## 测试工具

- cURL
- Postman
- Pytest + Requests

## 测试点

- 参数

输入：比如格式、类型、名称等、长度不一致，边界值、为空、重复等

返回：字段类型，字段值，是否缺少字段

- 权限

比如下单功能要依赖于登陆，否则应该返回异常

- 安全

cookie、header

- 状态

状态码 200 只代表请求返回正常，不代表返回数据正确，还要看自定义的错误码是否符合预期

- 逻辑

## 自动生成用例

HTTP 存档格式（HTTP Archive format, HAR）

HAR to JSON

```python
import json

def har2json(har_file, json_file):
    with open(har_file, "r", encoding='UTF-8') as f:
        json_dict = json.load(f)
        result = json_dict.get("log", {}).get("entries", [])

    with open(json_file, "w", encoding='UTF-8') as f:
        list = []
        list_url = []
        for i in result:
            """
            {
                "startedDateTime": "",
                "time": "",
                "request": {
                    "method": "",
                    "url": "",
                    "httpVersion": "",
                    "cookies": [],
                    "headers": [],
                    "queryString": [],
                    "postData": {},
                    "headersSize": 0,
                    "bodySize": 0
                },
                "response": {},
                "serverIPAddress": "",
                "cache": {},
                "timings": {}
            }
            """
            _dict = {}
            rq = i["request"]

            if rq["url"] in list_url:
                continue
            else:
                list_url.append(rq["url"])
                _dict["url"] = rq["url"]
                _dict["method"] = rq["method"]
                
                if rq["method"] == "GET":
                    _dict["payload"] = rq["queryString"][0] if rq.get("queryString", "") else ""
                elif rq["method"] == "POST":
                    _dict["payload"] = rq["postData"]["text"] if rq.get("postData", "") else ""
                else:
                    _dict["payload"] = ""
                list.append(_dict)
        json.dump(list, f, ensure_ascii=False)



if __name__ == "__main__":
    har2json("./har/content.har", "./har/case.json")
```
