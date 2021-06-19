# Pytest

官方文档：<https://docs.pytest.org>

安装：`pip install pytest`

帮助：`pytest --help`

## Case编写规范

`test_xxx.py` 或 `xxx_test.py`

```python
def setup_module():
    print("模块开始执行一次")


def teardown_module():
    print("模块结束执行一次")


def setup_function():
    print("每个函数前执行一次")


def teardown_function():
    print("每个函数后执行一次")


def test_func1():
    print("func1")
    assert a == b  # 断言


def test_func2():
    print("func2")


class TestDemo:
    # 不能写init方法

    def setup_class(cls):
        cls.k = "哈哈"  # 前置方法中的属性可以给其他方法调用
        print("类开始执行一次")


    def teardown_class(cls):
        print("类结束执行一次")


    def setup_method(cls):
        print("每个方法前执行一次")


    def teardown_method(cls):
        print("每个方法后执行一次")


    # 等价于setup_method的简写
    def setup(cls):
        print(cls.k)  # 调用前置方法中的属性
        print("每个方法前执行一次")


    # 等价于teardown_method的简写
    def teardown(cls):
        print("每个方法后执行一次")

    
    def test_method1(self):
        print(self.k)  # 调用前置方法中的属性
        print("method1")


    def test_method2(self):
        print("method2")
```

## Fixture

```python
# 可同时定义多个fixture，作用于不同范围，多个之间还可以互相调用
# fixture如果写在conftest.py文件中，则可以被同级目录多个文件一起调用
@pytest.fixture(scope="function", autouse=False, params=None, ids=None, name=None)
# 为区别于用例，函数命名不能以test开头
def login():
  print("登入)  # yield前面的类似于setup_xx
  yield

  print("登出)  # yield后面的类似于teardown_xx

"""
scope  作用范围
  session 或 package  多个文件共调用一次，通常把fixture写在conftest.py文件中
  module  模块级，类似于setup/teardown_module
  class  类级别，类似于setup/teardown_function + setup/teardown_class
  function  函数或者方法级（默认），类似于setup/teardown_function + setup/teardown_method

autouse
  False  默认，只会在传入函数名的case调用
  True  无需传参，自动为scope范围内的所有case调用，但不能给case返回值

params  可以用list指定哪些case使用fixture
ids  对应params的id
name  fixture的名称，默认就是函数名
"""

def test_01(login):  # 传入fixture函数名login作为参数，可传入多个
  print("case_01)  # 先登入，然后运行case，最后登出


# 在默认情况下，如果想让类中每一个方法都调用fixture，挨个传入login比较麻烦
# 此时则可以使用usefixtures装饰器让类中每个函数调用
# 不过这种方式与autouse=Ture一样无法给case返回值
@pytest.mark.usefixtures("login")  # 注意：login要写在引号内，可传入多个
class TestDemo:
  pass
```

```python
import pytest

@pytest.fixture()
def login():
    c = 3
    return c

_list=[[1,2], [10,20]]
@pytest.mark.parametrize("a,b", _list)
def test_01(a, b, login):
    print(a + b + login)
```

## 参数化

`@pytest.mark.parametrize(argnames, argvalues)`

argnames 被参数化的变量，形式："k1,k2"（字符串中逗号分隔变量）

argvalues 与变量一一对应的一组值，可以传入一个集合

- 普通形式

```python
# 需要导入pytest
import pytest

_list = [
  (1, 2),
  (4, 10),
  (7, 20)
  ]

@pytest.mark.parametrize("a,b", _list)
def test_demo(a, b):
    print(a+b)
```

- 笛卡尔积形式

```python
@pytest.mark.parametrize("a", [1,2,3])
@pytest.mark.parametrize("b", [4,5])
# 会产生3x2=6条case
def test_demo(a, b):
    print(a+b)
```

- 数据驱动

```yaml
-
 - 1
 - 2
-
 - 3
 - 4
```

```python
_list = yaml.safe_load(open(yaml_file))  # [[1,2], [3,4]]

@pytest.mark.parametrize(("a","b"), _list)
def test_demo(a, b):
    print(a+b)
```

- 使用fixture参数化

```python
import pytest

@pytest.fixture(params=[1,2,3])
def login(request):  # 固定参数
  return request.param  # 固定数据返回方式

def test_01(login):
  print(login + 1)  # 每条用例可以获得params中的一个值
```

- parametrize + fixture 形式

```python
import pytest

@pytest.fixture()
def login(request):
    return request.param

_list=[1,2,3]
# indirect=True时，argnames可以传入一个fixture函数
@pytest.mark.parametrize("login", _list, indirect=True)
def test_01(login):
    print(login+1)
```

## 运行

- 文件中执行：`pytest.main()`

```python
if __name__ == '__main__':
    pytest.main(["test_xx.py::类/函数", "-参数"])
```

- 命令行执行：`pytest` 或 `py.test`

会收集所有符合编写规范的文件以及文件内的函数和类以及方法，然后根据参数等略过不需要执行的

collected 7 items / 6 deselected / 1 selected

![4663921b77def085d62ac258dac1c94](http://image.zuoright.com/4663921b77def085d62ac258dac1c94.png)

## 测试范围

- 测试指定文件：`pytest test_x.py`
- 测试指定类/函数：`pytest test_x.py::类名/函数名`
- 测试指定方法：`pytest test_x.py::类名::方法名`
- 模糊匹配(不区分大小写)：`pytest -k 表达式`
  ```shell
  pytest -k a  # 测试【包含】a的case
  pytest -k "not a"  # 测试【不包含】a的case
  pytest -k "a or b"  # 测试包含a【或】b的case
  pytest -k "a and b"  # 测试【既包含】a【又包含】b的case
  pytest -k "a and not b"  # 测试【包含】a【但不包含】b的case

  ```
- 测试带`@pytest.mark.xxx`装饰器(标记)的Case：`pytest -m xxx`

## 测试失败，怎么处理

- 遇到任一条用例失败立即退出：`pytest -x`
- 遇到n条失败后退出：`pytest --maxfail=n`

## 调试信息

- verbose 罗嗦
![1f5e32c3bbf3b6e7a345b4dfaa5f72e](http://image.zuoright.com/1f5e32c3bbf3b6e7a345b4dfaa5f72e.png)

![f47d5c603ff0862910a548ead5df0ec](http://image.zuoright.com/f47d5c603ff0862910a548ead5df0ec.png)

- quiet 安静

![0b6becba62a5c6685a92aa82a50b191](http://image.zuoright.com/0b6becba62a5c6685a92aa82a50b191.png)

## 插件

插件列表：<https://docs.pytest.org/en/latest/reference/plugin_list.html>

### 指定用例执行顺序

`pip install pytest-ordering`

pytest默认乱序加载，可以用`pytest.mark.run(order=n)`设置顺序(1、2、3、-1)

### 并发执行

```shell
pip install pytest-xdist

pytest -n auto  # 自动检测系统cpu数量并发执行
pytest -n 4  # 指定4个cpu并发执行
```

### 生成测试报告

- pytest-html

```python
# 不加--self-contained-html时css样式是独立的
pytest --html=report.html --self-contained-html
```

- allure

```python
pip3 install allure-pytest
```

## 配置文件

- `conftest.py` 一些fixture配置
- `pytest.ini` pytest的主配置文件，可以改变pytest的默认行为
- `tox.ini` 与pytest.ini类似，用tox工具时候才有用
- `setup.cfg` 也是ini格式文件，影响setup.py的行为

## pytest.ini

- 指定哪些执行哪些不执行

```ini
[pytest]
markers = 
    do: do
    undo: undo
```

```python
@pytest.mark.do
def test_01()
  pass

@pytest.mark.undo
def test_02()
  pass
```

## 执行结果：

`.F.F..`中的`.`代表测试通过，`F`（Fail）代表测试失败

- fixture中的断言如果失败，结果会显示error
- case中的断言如果失败，结果会显示failed