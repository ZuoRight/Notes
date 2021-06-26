# Allure

一款支持多语言的测试报告生成框架：[官方文档](https://docs.qameta.io/allure/) 和 [报告demo](https://demo.qameta.io/allure/#)

手动安装驱动（运行依赖JRE）：[下载allure-commandline新版zip包](https://repo.maven.apache.org/maven2/io/qameta/allure/allure-commandline/)

- Windows：解压到非中文路径下，把bin目录添加到系统环境变量PATH中。
- Mac：解压到usr/local/下，把bin路径添加到环境变量中。另外还可以用`brew install allure`快速安装

## Pytest插件

`pip install allure-pytest`


```shell
# 生成报告到指定路径
pytest --alluredir=tmp/my_allure_results

# 运行allure查看报告
allure serve tmp/my_allure_results
```

```shell
# 把报告转成html格式（-o指定路径，--clean如果存在先清除）
allrue generate tmp/my_allure_results [-o tmp/path --clean]
# html格式的报告不能直接打开，需用此命令打开
allure open -h 127.0.0.1 -p 8883 tmp/path
```

## 常用特性


```python
import allure

@allure.testcase("case_link_url", "测试链接")

@allure.feature("功能")
@allure.story("子功能")

@allure.title("用例标题")
@allure.step("步骤，放在类/方法上面")
with allure.step("步骤，放在方法内")

@allure.severity(allure.severity_level.级别)
"""
Blocker 中断 影响下一步
Critical 临界 功能点缺失
Normal 正常 一般bug
Minor 次要 比如UI错误
Trival 轻微 比如文案不规范
"""
```

```shell
# 测试指定功能的case
pytest --allure-features="xxx"
pytest --allure-stories="xxx"
# 测试指定级别的Case
pytest --allure-severities critical,normal
```

## 添加附件

- 附件直接显示在报告中

```python
@allure.attach(body, name, attachment_type, extension)
"""
body：附件
name：附件名
attachment_type：附件类型(allure.attachment_type.xxx)
extension：附件扩展名
"""
```

- 附件以路径形式显示在报告中

```python
@allure.attach.file(source, name, attachment_type, extension)
"""
source：附件路径
其它同上
"""
```