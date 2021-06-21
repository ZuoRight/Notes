# Allure

一款支持多语言的测试报告生成框架：[官方文档](https://docs.qameta.io/allure/) 和 [报告demo](https://demo.qameta.io/allure/#)

手动安装驱动（运行依赖JRE）：[下载allure-commandline新版zip包](https://repo.maven.apache.org/maven2/io/qameta/allure/allure-commandline/)

- Windows：解压到非中文路径下，把bin目录添加到系统环境变量PATH中。
- Mac：解压到usr/local/下，把bin路径添加到环境变量中。另外还可以用`brew install allure`快速安装

Pytest插件

```shell
# 安装
pip install allure-pytest

# 生成报告到指定路径
pytest --alluredir=tmp/my_allure_results

# 运行allure查看报告
allure serve tmp/my_allure_results
# 把报告转成html格式（-o指定路径，--clean如果存在先清除）
allrue generate tmp/my_allure_results [-o tmp/path --clean]
# html格式的报告不能直接打开，需用此命令打开
allure open -h 127.0.0.1 -p 8883 tmp/path
```

## 常用特性

- `@allure.testcase("url")`  用例标识，可以填写case管理的地址
- `@allure.feature("功能")`
- `@allure.story("子功能")`
- `@allure.step("步骤，放在类/方法上面")` 或 `with allure.step("步骤，放在方法内")`
- `@allure.attach.file("附加信息")`


测试指定功能/子功能：`pytest --allure-features/stories="xxx"`