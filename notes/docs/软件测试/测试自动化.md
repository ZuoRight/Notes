---
comments: true
hide:
  - feedback
---

# 测试自动化

- 框架选择
- 测试驱动
- 数据存取
- 依赖处理
- 结果断言
- 测试报告
- 持续集成

## 框架选择

- API自动化

如果是HTTP协议接口首选第三方库`Requests`，RPC协议可以选用第三方库`ZeroRPC`，WebSocket协议可以使用系统内置的`webSocket`

- UI自动化

Web端可选`Selenium`，移动端可选`Appium`，底层其实都是`Webdriver`

## 测试驱动

首选Pytest

参数化、数据驱动、fixture做接口依赖，allure测试报告插件

## 数据存取

数据包括：测试用例、配置文件、日志等等

存取数据的格式可以是：json、yaml、excel、database等等

如果是类表格形式的数据，比如excel、csv以及list类型的json、yaml等，可以使用tablib这个库来统一处理

使用非数据库格式存取用例的缺点是，增减内容都需要重新提交代码，而且多人协作会有版本不一致的问题

使用数据库相对麻烦一些，但是可以集成Django等框架借助平台来更好的管理用例

关于数据库的选型，SQLite比较小巧灵活，但原生支持数据类型较少，MySQL新版本原生支持Json格式存储

- 获取单元格内容（`get_cell_value`）

```python
"""
前提：case_id唯一
"""

# 方法1，基于openpyxl
获取表中所有数据：sh
获取col_index，依赖header_field，自定义方法：循环某行的值做匹配，然后用内置方法cell.column获取
获取row_index，依赖case_id，自定义方法：循环case_id列的值做匹配，然后用内置方法cell.row获取
根据row_index和col_index获取cell_value，内置方法：sh.cell(row_index, col_index).value

# 方法2，基于tablib
获取表中所有数据：data
获取col_index，依赖header_field：data.headers.index("header_field")
获取col_list， 依赖header_field或col_index：data[header_field]
获取row_index，依赖col_list：col_list.index(case_id)
获取row_list，依赖row_index，data[row_index]
获取cell_value，依赖row_list和col_index，内置方法：row_list[col_index]
```

- 如果从Excel读取的payload其实是字符串格式的，需要转换并反序列化为dict

```python
import json
import re

def payload_to_dict(payload_init):
    if isinstance(payload_init, str):
        try:
            """
            反序列化前需要做一些json标准化格式处理
            单引号转换为双引号
            bool值转换为json格式的
            """
            payload_str_json = payload_init.replace('\'', '\"')
            reg_true = re.compile('True', re.IGNORECASE)
            payload_str_json = reg_true.sub('true', payload_str_json)
            reg_false = re.compile('False', re.IGNORECASE)
            payload_str_json = reg_false.sub('false', payload_str_json)
            # 反序列化
            payload_dict = json.loads(payload_str_json)
        except json.decoder.JSONDecodeError as e:
            logging.error(f"请检查数据格式, {e}")
        else:
            return payload_dict
    return payload_init

payload_str = case[headers_index("payload")]
payload_dict = payload_to_dict(payload_str)
```

- 借助jsonpath解析json，获取指定字段的值

```python
import json
import jsonpath

def load_json(path):
    with open(path, "r", encoding='utf_8') as f:
        obj = json.load(f)
    return obj

def read_from_json(data, key, value_type=0):
    if isinstance(object, dict):
        key_rex = "$." + key
        result = jsonpath.jsonpath(data, key_rex)
        # result是一个list, 有可能会解析出多个列表，待优化
        if result:
            # 默认返回list
            if value_type == 0:
                return result
            # 返回list中的值
            elif value_type == 1:
                return result[0]
            else:
                return None
        else:
            return None
    else:
        print("data类型非json")
        return None
```

## 依赖处理

- 使用`$`符和`case_id`处理依赖

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

## 结果断言（待整理）

主要使用deepdiff模块二次封装

可校验字段类型，字段值，是否缺少字段，还可以忽略校验

```json
{
    "infoq_002": {
        "expect_response": {
            "data":{
                "content": "正常评论"
            }
        },
        "is_check_type": 1,
        "is_check_value": 1,
        "uncheck_list_type": [],
        "uncheck_list_value": []
    }
}
```

```python
import json
import jsonpath
from deepdiff import DeepDiff


# 从json文件读取json字段对应的值
def get_value_from_path(json_path, key):
    key_rex = "$." + key
    with open(json_path, "r", encoding='UTF-8') as f:
        json_obj = json.load(f)
        result = jsonpath.jsonpath(json_obj, key_rex)  # 得到一个单值列表
        if result:
            for i in result:  # 从列表中循环出来
                return i
        else:
            return None

# 从字典对象读取json字段对应的值
def get_value_from_obj(json_obj, key):
    key_rex = "$." + key
    result = jsonpath.jsonpath(json_obj, key_rex)  # 得到一个单值列表
    if result:
        for i in result:  # 从列表中循环出来
            return i
    else:
        return None

class HandleDiff():
    def __init__(self, expected_dict, actually_dict):
        self.expected_dict = expected_dict
        self.actually_dict = actually_dict

        self.first_key = self.loop(self.expected_dict)
        self.first_key_root = self.convert_to_root(self.first_key)

        self.result = DeepDiff(self.expected_dict, self.actually_dict, ignore_order=True, ignore_string_case=True, verbose_level=0).to_dict()
        self.result.pop("dictionary_item_added", None)
        # print("Diff1：", self.result, "\n", "======")

    # 循环dict中的每一个key及子key，返回list
    @staticmethod
    def loop(x):
        _list=[]

        def inner_loop(key):
            value = get_value_from_obj(x, key)
            if type(value) == dict:
                for i in value:
                    r = f"{key}.{i}"
                    _list.append(r)
                    inner_loop(r)

        for i in x:
            _list.append(i)
            inner_loop(i)

        return _list

    # 将a.b形式的key转换为root[a][b]形式
    @staticmethod
    def convert_to_root(x):
        _list = []
        for key in x:
            temp = []
            for i in key.split("."):
                j = "['" + i + "']"
                temp.append(j)
            r = "root" + "".join(temp)
            _list.append(r)
        return(_list)

    # 整合
    def check(self, is_check_type=0, is_check_value=0, uncheck_list_type=[], uncheck_list_value=[]):
        report = {}
        if self.result:
            dict_item_removed = self.result.get("dictionary_item_removed")
            type_changes = self.result.get("type_changes")

            if dict_item_removed:
                temp = []
                for i in dict_item_removed:
                    if i in self.first_key_root:
                        temp.append(i)
                if temp:
                    report["实际结果比预期缺少字段"] = temp

            if is_check_type:
                if type_changes:
                    if uncheck_list_type:
                        uncheck_list_root = self.convert_to_root(uncheck_list_type)
                        for i in uncheck_list_root:
                            if i in type_changes:
                                type_changes.pop(i, None)
                    report["实际结果中值的类型与预期不符"] = type_changes

            if is_check_value:
                if uncheck_list_value:
                    uncheck_list_root = self.convert_to_root(uncheck_list_value)
                    self.result = DeepDiff(self.expected_dict, self.actually_dict, exclude_paths=uncheck_list_root , ignore_order=True, ignore_string_case=True, verbose_level=0).to_dict()

                # 这里很关键
                x1 = report.get("实际结果比预期缺少字段", "")
                x2 = self.result.get("dictionary_item_removed", "")
                report["实际结果比预期缺少字段"] = set(x1) | set(x2)

                self.result.pop("dictionary_item_removed", None)
                self.result.pop("type_changes", None)
                self.result.pop("dictionary_item_added", None)
                report["实际结果中的值与预期不一致"] = self.result
        # 去除一些空的情况：{'实际结果中值的类型与预期不符': {}, '实际结果比预期缺少字段': set(), '实际结果中的值与预期不一致': {}}
        empty_key = [k for k,v in report.items() if not v]
        for k in empty_key:
            report.pop(k, None)

        return report



if __name__ == "__main__":
    old = {
        "a":1,
        "b":[2,3,4],
        "c":{
            "d":4,
            "e":[{
                "id": 81,
                "name": "区块链",
                "alias": "blockchain"
                },2]
            },
        "f":5
    }

    new = {
        "a":1,
        "b":[2,3,4],
        "c":{
            "d":4,
            "e":[{
                "id": 81,
                "name": "区块链",
                "alias": "blockchain"
                },2]
            },
        "f":5,
        "g":6
    }

    is_check_type = 1
    is_check_value = 1
    uncheck_list_type = []
    uncheck_list_value = []

    diff_json = HandleDiff(old, new)
    report = diff_json.check(is_check_type, is_check_value, uncheck_list_type, uncheck_list_value)
    print(report)
```

```python
def check(self, index, case_id, run_response):
    case_load = get_value_from_path(self.case_load_path, f"{case_id}")
    col_index_result = self.get_col_index("test_result")
    expect_response = case_load.get("expect_response")
    logging.info(f"{case_id}期望返回: expect_response")
    if expect_response:
        is_check_type = case_load.get("is_check_type")
        is_check_value = case_load.get("is_check_value")
        uncheck_list_type = case_load.get("uncheck_list_type")
        uncheck_list_value = case_load.get("uncheck_list_value")

        # 结果对比
        # 这里run_response要使用json格式
        diff_json = HandleDiff(expect_response, run_response.json())
        report = diff_json.check(is_check_type, is_check_value, uncheck_list_type, uncheck_list_value)

        # 将测试结果写入Excel
        if report:
            # 标失败色
            self.write_test_result(index, col_index_result,  "Fail", "ff4c00")
            # 失败原因
            col_index_fail_cause = self.get_col_index("fail_cause")
            self.write_test_result(index, col_index_fail_cause, f"{report}")
            # extra
            col_index_extra = self.get_col_index("extra")
            self.write_test_result(index, col_index_extra, f"request-id: {run_response['extra']['request-id']}")
        else:
            # 标成功色
            self.write_test_result(index, col_index_result, "Pass", "00e500")
    else:
        self.write_test_result(index, col_index_result, "未配置expect_response，无法判断测试是否成功")
```
