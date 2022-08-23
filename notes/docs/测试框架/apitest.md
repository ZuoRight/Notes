# 接口测试框架

- 请求库
- 自动化
- 数据存取
- 依赖处理
- 测试报告
- 持续集成

## 请求库

如果是HTTP协议接口首选第三方库`requests`

RPC协议可以选用第三方库`zerorpc`

WebSocket协议可以使用系统内置的`webSocket`

## 自动化

首选Pytest

参数化、数据驱动、fixture做接口依赖，allure测试报告插件

## 数据存取

数据包括：测试用例、配置文件、日志等等

存取数据的格式可以是：json、yaml、excel、database等等

如果是类表格形式的数据，比如excel、csv以及list类型的json、yaml等，可以使用tablib这个库来统一处理

使用非数据库格式存取用例的缺点是，增减内容都需要重新提交代码，而且多人协作会有版本不一致的问题

使用数据库相对麻烦一些，但是可以集成Django等框架借助平台来更好的管理用例

关于数据库的选型，SQLite比较小巧灵活，但原生支持数据类型较少，MySQL新版本原生支持Json格式存储

## 依赖处理

- 从json中读取依赖

```json
{
    "infoq_004": {
        "payload": {
            "$aid": {"infoq_002": "data.aid"},
            "$comment_id": {"infoq_002": "data.id"},
            "content": "正常回复"
        }
    }
}
```

```python
# 从json文件中获取case依赖数据
case_load = get_value_from_path(self.case_load_path, f"{case_id}")
# 从依赖数据中取出payload
payload = case_load.get("payload")
# 判断payload是否存在
if payload:
    logging.info(f"{case_id}的请求参数: {payload}")
    # 判断payload是否有依赖
    for k,v in payload.items():
        # 获取依赖
        if k[0] == "$":
            for depended_case_id, depended_key in v.items():
                logging.info(f"{'-'*10}")
                logging.info(f"需要依赖{depended_case_id}的「{depended_key}」字段")
                # 逐层递归查看依赖是否还有依赖，请求依赖接口获取数据
                depended_case_index = self.get_row_index(depended_case_id)
                depend_case_response = self.run_case(depended_case_index, depended_case_id)
                logging.info(f"{depended_case_id}的返回结果: {depend_case_response.json()}")
                logging.info(f"{depended_case_id}的返回状态码: {depend_case_response.status_code}")
                logging.info(f"{'-'*10}")
                # 根据被依赖的key去依赖case的返回结果中取到对应的依赖值，赋值给依赖key
                # 这里的depend_case_response需要使用json格式
                payload[k.lstrip("$")] = get_value_from_obj(depend_case_response.json(), depended_key)
            # 因为前面size增加了，这里size减小相当于size没变，所以迭代时不会报错
            payload.pop(k)
            logging.info(f"{case_id}的请求参数(替换依赖后): {payload}")
else:
    payload = None
    logging.info(f"{case_id}的请求参数: {payload}")
```

- 从excel中读取依赖

![20210817141422](http://image.zuoright.com/20210817141422.png)

```python
# 从excel获取payload
payload_str = self.get_field(index, "payload")
if payload_str:
    payload = json.loads(payload_str)
    logging.info(f"{case_id}的请求参数: {payload}")
    # 判断payload是否有依赖
    for k,v in payload.items():
        # 获取依赖
        if k[0] == "$":
            for depended_case_id, depended_key in v.items():
                logging.info(f"{'-'*10}")
                logging.info(f"需要依赖{depended_case_id}的「{depended_key}」字段")
                # 逐层递归查看依赖是否还有依赖，请求依赖接口获取数据
                depended_case_index = self.get_row_index(depended_case_id)
                depend_case_response = self.run_case(depended_case_index, depended_case_id)
                logging.info(f"{depended_case_id}的返回结果: {depend_case_response.json()}")
                logging.info(f"{depended_case_id}的返回状态码: {depend_case_response.status_code}")
                logging.info(f"{'-'*10}")
                # 根据被依赖的key去依赖case的返回结果中取到对应的依赖值，赋值给依赖key
                # 这里的depend_case_response需要使用json格式
                payload[k.lstrip("$")] = get_value_from_obj(depend_case_response.json(), depended_key)
            # 因为前面size增加了，这里size减小相当于size没变，所以迭代时不会报错
            payload.pop(k)
            logging.info(f"{case_id}的请求参数(替换依赖后): {payload}")
else:
    payload = None
    logging.info(f"{case_id}的请求参数: {payload}")
```

## 数据校验模块

主要封装了deepdiff模块

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
