# HTTPRunner

> 此框架没有深入研究过，仅大概了解，简单看了下源码，跑Demo体验了一下

官网：<https://httprunner.com/>

安装3.0版本：`pip3 install git+https://github.com/httprunner/httprunner.git@master`

![20220804104402](https://image.zuoright.com/20220804104402.png)

![20220804104436](https://image.zuoright.com/20220804104436.png)

![20220804104451](https://image.zuoright.com/20220804104451.png)

```shell
# 主要命令：httprunner
(env) bash-5.0$ httprunner -h

usage: httprunner [-h] [-V] {run,startproject,har2case,make} ...

One-stop solution for HTTP(S) testing.

positional arguments:
  {run,startproject,har2case,make}
                        sub-command help
    run                 Make HttpRunner testcases and run with pytest.
    startproject        Create a new project with template structure.
    har2case            Convert HAR(HTTP Archive) to YAML/JSON testcases for HttpRunner.
    make                Convert YAML/JSON testcases to pytest cases.

optional arguments:
  -h, --help            show this help message and exit
  -V, --version         show version


# 创建项目
(env) bash-5.0$ httprunner startproject infoq
2021-12-01 15:44:36.984 | INFO     | httprunner.scaffold:create_scaffold:43 - Create new project: infoq
Project Root Dir: /Users/7c/zuoright/demo-httprunner/infoq

created folder: infoq
created folder: infoq/har
created folder: infoq/testcases
created folder: infoq/reports
created file: infoq/testcases/demo_testcase_request.yml
created file: infoq/testcases/demo_testcase_ref.yml
created file: infoq/debugtalk.py
created file: infoq/.env
created file: infoq/.gitignore

$ tree infoq -a
2021-12-01 15:44:36.993 | WARNING  | httprunner.scaffold:show_tree:29 - tree command not exists, ignore.
Sentry is attempting to send 0 pending error messages
Waiting up to 2 seconds
Press Ctrl-C to quit
```

```shell
$ tree demo -a
demo
├── .env
├── .gitignore
├── debugtalk.py
├── har
├── reports
└── testcases
    ├── demo_testcase_ref.yml
    └── demo_testcase_request.yml
```

执行用例：`hrun infoq`

> hrun = make + pytest， 是httprunner run的缩写，封装了pytest，所以支持pytest的功能参数，执行非pytest格式的用例会自动先make为pytest格式

```shell
$ tree httprunner_demo -a  
httprunner_demo
├── .env
├── .gitignore
├── data
│   ├── account.csv
│   └── vip.csv
├── debugtalk.py
├── har
├── logs
│   └── 2aad97e8-2c00-46a6-8897-e129e0d741be.run.log
├── reports
│   ├── assets
│   │   └── style.css
│   └── demo.html
├── testcases
│   ├── __init__.py
│   ├── demo_testcase_request.yml
│   ├── demo_testcase_request_test.py
└── testsuites
    └── suite.yml
```

HttpRunner v3.x 支持3种用例格式：pytest、YAML和JSON

> 关于为什么从HttpRunner v3.x开始，作者比较建议python脚本编写用例。主要是编写Python可以复用IDE的能力，包括语法提示、自动补全，这些都会是Yaml/Json比较欠缺的。

![20220804104511](https://image.zuoright.com/20220804104511.png)

测试用例字段解释：[https://ontheway.cool/HttpRunner3DocsForCN/concepts/testcase_structure](https://ontheway.cool/HttpRunner3DocsForCN/concepts/testcase_structure/)

可以使用抓包工具录制请求，然后导出为har文件，再使用har2case命令转换为指定格式的case，默认转换为pytest格式

```shell
(env) bash-5.0$ har2case -h

usage: har2case har2case [-h] [-2y] [-2j] [--filter FILTER] [--exclude EXCLUDE] [har_source_file]

positional arguments:
  har_source_file       Specify HAR source file

optional arguments:
  -h, --help            show this help message and exit
  -2y, --to-yml, --to-yaml
                        Convert to YAML format, if not specified, convert to pytest format by default.
  -2j, --to-json        Convert to JSON format, if not specified, convert to pytest format by default.
  --filter FILTER       Specify filter keyword, only url include filter string will be converted.
  --exclude EXCLUDE     Specify exclude keyword, url that includes exclude string will be ignored, multiple keywords can be joined with '|'
```
