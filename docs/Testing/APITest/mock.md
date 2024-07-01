# 依赖处理

- 全局变量
- Pytest 的 Fixture
- `$` 调用 api 或者 case_id，以及自定义处理函数
- Mock，当一个接口依赖另一个接口，由于被依赖接口并不是主要测试对象，则可以使用假数据 Mock 掉
    - Charles map local/remote
    - Ngnix 反向代理
    - Fake
    - 打桩
    - Swagger

## 使用 `$` 和 `case_id` 处理依赖

```python
from utils.handle_data import GetData
from utils.handle_io import read_from_json, payload_to_dict


cases = GetData("data/demo.xlsx")
req = HandleRequest()


def run_case(case_id):
    row_index = cases.get_row_index("case_id", case_id)
    case = cases.get_row_list(row_index)
    payload_str = case[cases.get_col_index("payload")]
    payload = loads_json(payload_str)  # 将json_str 反序列化为python的dict或list
    """
    payload = {
        "$aid": {  # 带有$符的字段表示其值依赖了其它case的返回值
            "case_id": "007",  # 依赖的case_id
            "key": "data.xxx",  # 依赖的字段，使用jsonpath解析
            "value_type": 0,  # jsonpath默认返回list，0 表示返回list结果（默认），1 取list[0]
            "function": ""  # 依赖的值可能需要进一步处理
        }
        "content": "xxx"
    }
    """
    if payload:
        if isinstance(payload, dict):
            self.build_payload(payload)
        elif isinstance(payload, list):
            payload_list = []
            for payload_sub in payload:
                payload_sub_dict = self.build_payload(payload_sub)
                payload_list.append(payload_sub_dict)
        else:
            print("Payload格式错误, 请检查")

    # 调用http_request方法请求api，具体传参可根据业务调整
    method = case[self.get_col_index("method")]
    api = case[self.get_col_index("api")]
    resp = http_request(method, api, payload)
    return resp


def build_payload(self, payload: dict) -> dict:
    cache = {}
    # 迭代时dict大小不能被改变，否则会报错，用list包裹可以避免此问题
    # list({"a":1,"b":2}.items()) 将dict变为list [('a', 1), ('b', 2)]
    for k,v in list(payload.items()):
        if k[0] == "$":
            depend_case_id = v.get("case_id")
            depend_key = v.get("key")
            depend_value_type = v.get("value_type", 0)
            depend_function = v.get("function")
            """
            将depend_case_id存到缓存cache
            如果多个字段都依赖了相同的case，则复用缓存，不需要重复请求
            """
            depend_case_response = cache.get(depend_case_id)
            if not depend_case_response:
                depend_case_response = self.run_case(depend_case_id)  # 递归
                """
                如果依赖没有依赖则直接获取依赖响应，缓存
                如果依赖还有依赖则继续递归，直到最底层没有依赖的接口
                """
                cache[depend_case_id] = depend_case_response  
            # 根据依赖字段从依赖响应中获取依赖值
            depend_data = read_from_json(depend_case_response.json(), depend_key, depend_value_type)
            # 依赖值可能需要进一步处理
            if depend_function:
                depend_function = eval(depend_function)  # 此时depended_function的值还是一个str，需要先还原成函数
                depend_data = depend_function(depend_data)  # 处理后的返回覆盖未处理的
            # 添加不带$的字段
            payload[k.lstrip("$")] = depend_data
            # 删除带$的字段
            payload.pop(k)
    return payload
```

## Stub

打桩：一般在做单元或集成测试时，如果某个程序单元的某条语句，需要调用的一个外部函数还没有设计、编码、调试完成的话，可以只让它简单地返回几个支持测试用例的值就可以了，这种状态的外部函数一般就叫做“打桩”

- 模拟外部依赖：打桩用于模拟外部服务、数据库或其他模块，以便在没有这些依赖的情况下进行单元测试。
- 测试隔离：通过替换实际组件，使得测试可以集中在具体的模块上，避免受到外部系统的影响。
- 控制测试环境：通过打桩，可以精确控制测试输入和输出，验证系统在各种条件下的行为。

### unittest.mock

```python
from unittest.mock import Mock

# 创建一个 Mock 对象
mock = Mock()

# 调用 mock 对象的方法
mock.some_method()

# 验证方法是否被调用
print(mock.some_method.called)  # 输出: True
```

### pytest-mock

