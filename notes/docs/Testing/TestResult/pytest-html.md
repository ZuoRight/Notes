# pytest-html

```shell
pip install pytest-html

# --self-contained-html css样式混在html中，不加则独立
pytest --html=report.html --self-contained-html
```

可通过 `conftest.py` 文件修改一些内容

> 修改与汉化：<https://www.cnblogs.com/linuxchao/p/linuxchao-pytest-html.html>

```python
import pytest
from py._xmlgen import html


# 测试报告标题
def pytest_html_report_title(report):
    report.title = "接口自动化测试报告"

# 修改Environment
def pytest_configure(config):
    config._metadata.pop("Plugins")  # 删除
    config._metadata["Data Path"] = "data/"  # 添加

# 修改Summary
@pytest.mark.optionalhook
def pytest_html_results_summary(prefix):
    prefix.extend([html.p("测试项目: EE")])

# 删除header
@pytest.mark.optionalhook
def pytest_html_results_table_header(cells):
    cells.pop(-1)  # link列

# 删除row
@pytest.mark.optionalhook
def pytest_html_results_table_row(report, cells):
    cells.pop(-1)  # link row

# 解决中文乱码
# @pytest.mark.hookwrapper
# def pytest_runtest_makereport(item):
#     outcome = yield
#     report = outcome.get_result()
#     getattr(report, 'extra', [])
#     report.nodeid = report.nodeid.encode("utf-8").decode("unicode_escape")
```
