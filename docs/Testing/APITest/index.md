# 引言

API, Application Programming Interface

## 测试工具

- cURL 命令行
- Postman 图形化
- [Bruno](https://github.com/usebruno/bruno) 是 Postman 的开源轻量替代品，仅支持离线使用，不会云同步，确保了数据安全性
- Pytest + Requests 自动化

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
