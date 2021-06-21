# Allure

一款支持多语言的测试报告生成框架：[官方文档](https://docs.qameta.io/allure/) 和 [报告demo](https://demo.qameta.io/allure/#)

手动安装驱动（运行依赖JRE）：[下载allure-commandline新版zip包](https://repo.maven.apache.org/maven2/io/qameta/allure/allure-commandline/)

- Windows：解压到非中文路径下，把bin目录添加到系统环境变量PATH中。
- Mac：解压到usr/local/下，把bin路径添加到环境变量中。另外还可以用`brew install allure`快速安装

## Pytest插件

```shell
pip install allure-pytest  # 安装

pytest --alluredir=/tmp/my_allure_results  # 生成报告到指定路径

allure serve /tmp/my_allure_results  # 运行allure查看报告
```
