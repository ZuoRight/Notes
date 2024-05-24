---
comments: true
hide:
  - feedback
---

# Allure

[官方网站](https://qameta.io/allure-report/){ .md-button .md-button--primary }

一款支持多语言的测试报告生成框架，官方文档：<https://docs.qameta.io/allure-report/#_pytest>

## 本地安装

> 运行依赖JRE

[下载allure-commandline新版zip包](https://repo.maven.apache.org/maven2/io/qameta/allure/allure-commandline/)

- Mac：解压到usr/local/下，把bin路径添加到环境变量中。还可以直接用`brew install allure`快速安装
- Windows：解压到非中文路径下，把bin目录添加到系统环境变量PATH中。

## Allure to Pytest

`pip install allure-pytest`

```shell
# 生成报告到指定路径（--clean-alluredir 清除之前运行产生的文件）
pytest --alluredir=tmp/allure-results [--clean-alluredir]

# 运行allure查看报告
allure serve tmp/allure-results
```

```shell
# 把报告转成html格式（-o 指定路径，--clean 清除之前运行产生的文件）
allrue generate tmp/allures [-o tmp/path] [--clean]
# html格式的报告不能直接打开，需用此命令打开
allure open -h 127.0.0.1 -p 8883 tmp/path
```

经验：前后两次执行同一文件+方法+传参相同时，生成的`allure-result/xxxx-result.json`文件中的name字段一致，后一次会变为前一次的retry，也就是所谓的覆盖，如果分布式/多线程/进程运行想最后生成到同一allure-result中不变成覆盖的情况，就要想办法改变name，文件名和方法名不变，只能改变传参，可以用fixture传入一个动态参数（必须使用到params），这样就变成平行的多条用例，而不是覆盖的一条用例。

吐槽：曾经遇到过`allure serve path`后页面展示缺少了某个模块的用例，但是`allure-results`中是有相关文件的，所以确定不是用例运行的问题，然后`allrue generate path`后`allure open path`是完整的，怀疑`allure serve path`有点什么bug，没有进一步研究，推荐`allure open path`。

## 常用特性

- 静态属性: 装饰器形式使用

![20220607201628](http://image.zuoright.com/20220607201628.png)

```python
import allure

@allure.testcase("case_link_url", "测试链接")

@allure.feature("功能")
@allure.story("子功能")

@allure.title("用例标题")
@allure.title("Parameterized test title: adding {param1} with {param2}")  # 可以使用变量

@allure.step("步骤")

@allure.severity(allure.severity_level.级别)
"""
Blocker 中断 影响下一步
Critical 临界 功能点缺失
Normal 正常 一般bug
Minor 次要 比如UI错误
Trival 轻微 比如文案不规范
"""
```

- 动态属性: 方法内使用

```python
# 大部分静态属性都可以变为动态属性，这里只列了两种举例
allure.dynamic.title('After a successful test finish, the title was replaced with this line.')
allure.dynamic.description('A final description.')

with allure.step("步骤，放在方法内"):
  pass
```

- 指定属性运行

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

## 页面优化

### 添加环境

如果要在测试报告概览-环境模块展示信息，可以在`allure-results`路径下新建一个`environment.properties`文件

> `.properties` 是java环境的一种配置文件格式

```properties
Browser=Chrome
Browser.Version=63.0
Stand=Production
```

### 添加分类

默认只显示了`failed`和`broken`两类，可以增加`skipped`等分类

```json
[
  {
    "name": "Ignored tests", 
    "matchedStatuses": ["skipped"] 
  },
  {
    "name": "Infrastructure problems",
    "matchedStatuses": ["broken", "failed"],
    "messageRegex": ".*bye-bye.*" 
  },
  {
    "name": "Outdated tests",
    "matchedStatuses": ["broken"],
    "traceRegex": ".*FileNotFoundException.*" 
  },
  {
    "name": "Product defects",
    "matchedStatuses": ["failed"]
  },
  {
    "name": "Test defects",
    "matchedStatuses": ["broken"]
  }
]
```

## Docker 服务

### allure-server

<https://github.com/kochetkov-ma/allure-server>

### allure-docker-service

<https://github.com/fescobar/allure-docker-service>

```shell
sudo docker run -d -p 5050:5050 \
    -e CHECK_RESULTS_EVERY_SECONDS=3 \
    -e KEEP_HISTORY=1 \
    -e OPTIMIZE_STORAGE=1 \
    -v ${PWD}/allure-results:/app/allure-results \
    -v ${PWD}/allure-reports:/app/default-reports \
    frankescobar/allure-docker-service

# 挂载目录${PWD}可以是任何位置，比如我这里是：/home/ubuntu/work/test_result
#   但${PWD}后面的路径以及对应容器中的路径，是不能改的
#   需要注意文件的权限，最好先手动创建好相应路径，并赋予一定的权限
#   /app/default-reports用于挂载存放历史报告
```

> 查看最新报告: <http://localhost:5050/allure-docker-service/latest-report>
>
> 常用API: <http://localhost:5050/>，比如删除历史记录和测试结果等

- CHECK_RESULTS_EVERY_SECONDS=3 为每3秒自动检查allure-results是否有文件更新
- CHECK_RESULTS_EVERY_SECONDS=None 则不检查，可以使用GET /generate-report主动生成，可指定Executors

```text
GET /generate-report?execution_name=自定义名字
GET /generate-report?execution_from=自定义链接
GET /generate-report?execution_type=图标（jenkins/gitlab/github/bamboo/teamcity）
```

- KEEP_HISTORY=1 保留历史和趋势（默认保留最新的20个），可使用API清除历史记录，可点击logo查看历史
- OPTIMIZE_STORAGE=1 将app.js和styles.css这两个不会变的静态文件放在容器/app/resources/中引用，减少default-reports存储
