---
comments: true
hide:
  - feedback
---

# 测试用例

所有人员参与需求评审 -> 测试人员编写测试用例 -> 所有人员参与用例评审 -> 开发人员按照用例，进行自测 -> 开发人员提测，测试人员进行测试

## 工具

- Excel

- Xmind

模版：测试模块 -> 测试点 -> 用例标题 -> 操作步骤 -> 预期结果

- SQL

## 黑盒测试方法

- 等价类划分
    - 有效等价类
    - 无效等价类
- 边界值分析
- 流程关系
    - 因果图
    - 判定表
    - 决策树，可以用流程图表示
- 错误推断

## 白盒测试方法

- 语句/路径覆盖：覆盖所有可能执行的路径
- 数据流覆盖
- 作用域覆盖

## 自动化测试用例原则

- 用例之间保持独立，不要有依赖关系
- 用例可以重复运行，可以随机运行
- 用例运行结果不会影响其它用例
- 用例结果要明确，成功或失败

## 自动生成用例

### 反射

```python
import unittest

def generate_test_case(a, b, expected):
    def test_case(self):
        calc = Calculator()
        self.assertEqual(calc.add(a, b), expected)
    return test_case

class TestCalculator(unittest.TestCase):
    pass

test_data = [
    (1, 2, 3),
    (-1, 1, 0),
    (-1, -1, -2)
]

for i, (a, b, expected) in enumerate(test_data):
    test_method = generate_test_case(a, b, expected)
    setattr(TestCalculator, f'test_add_{i}', test_method)


# 会动态生成如下代码
"""
class TestCalculator(unittest.TestCase):
    def test_add_0(self):
        calc = Calculator()
        self.assertEqual(calc.add(1, 2), 3)
    
    def test_add_1(self):
        pass

    def test_add_2(self):
        pass
"""


if __name__ == '__main__':
    unittest.main()
```

### 用例录制

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

### Swagger doc api

### Faker
