# Allure

[官方网站](https://qameta.io/allure-report/){ .md-button .md-button--primary }

一款支持多语言的测试报告生成框架，官方文档：<https://docs.qameta.io/allure-report/>


## 本地安装

> 运行依赖JRE

[下载allure-commandline新版zip包](https://repo.maven.apache.org/maven2/io/qameta/allure/allure-commandline/)

- Mac：解压到usr/local/下，把bin路径添加到环境变量中。还可以直接用`brew install allure`快速安装
- Windows：解压到非中文路径下，把bin目录添加到系统环境变量PATH中。

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

<https://docs.qameta.io/allure-report/#_pytest>

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

## Docker 服务

### allure-server

<https://github.com/kochetkov-ma/allure-server>

### allure-docker-service

<https://github.com/fescobar/allure-docker-service>

```shell
sudo docker run -d -p 5050:5050 -e CHECK_RESULTS_EVERY_SECONDS=3 -e KEEP_HISTORY=1 \
    -v ${PWD}/allure-results:/app/allure-results \
    -v ${PWD}/allure-reports:/app/default-reports \
    frankescobar/allure-docker-service

# 挂载目录${PWD}可以是任何位置，比如我这里是：/home/ubuntu/work/test_result
#   但${PWD}后面的路径以及对应容器中的路径，是不能改的
#   需要注意文件的权限，最好先手动创建好相应路径，并赋予一定的权限
#   /app/default-reports用于挂载存放历史报告

# 查看最新报告: http://localhost:5050/allure-docker-service/latest-report
# 一些常用API: http://localhost:5050/
```

- CHECK_RESULTS_EVERY_SECONDS=3 为每3秒自动检查allure-results是否有文件更新
- CHECK_RESULTS_EVERY_SECONDS=None 则不检查，可以使用GET /generate-report主动生成，可指定Executors

```text
GET /generate-report?execution_name=自定义名字
GET /generate-report?execution_from=自定义链接
GET /generate-report?execution_type=图标（jenkins/gitlab/github/bamboo/teamcity）
```

- KEEP_HISTORY=1 保留历史和趋势（默认保留最新的20个），可使用API清除历史记录，可点击logo查看历史
- OPTIMIZE_STORAGE: 1 优化default-reports存储，不会再每次存储app.js和styles.css这两个不会变的文件，而是放在容器中公共位置存储
