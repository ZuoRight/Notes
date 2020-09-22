# Pytest

## 固定格式

- 测试文件，以`test_`开头或`_test`结尾
- 测试类，以`Test`开头，且无需实例方法，但记得加cls或self
- 测试函数，以`test_`开头
- 断言，使用assert

## Case

```python
def setup_module():
  # 模块开始执行一次
  pass

def teardown_module():
  # 模块结束执行一次
  pass

def setup_function():
  # 每个case函数前都执行一次
  pass

def teardown_function():
  # 每个case函数后都执行一次
  pass
```

```python
class TestX():
  def setup_class(cls):
    # 类开始执行一次
    pass

  def teardown_class(self):
    # 类结束执行一次
    pass

  def setup_method(cls):
    # 类中，每个case前都执行一次，在fixture前
    pass

  def teardown_method(self):
    # 类中，每个case后都执行一次
    pass

  def setup(self):
    # 类中，每个case前都执行一次，在fixture和setup_method后，优先级最低

  def teardown(self):
      # 类中，每个case后都执行一次，在teardown_method前
    pass
  
  def test_1(self):
    pass
```

## Fixture 夹具

```python
# 可同时定义多个fixture，多个之间可以互相调用
@pytest.fixture(scope="function", autouse=False, params=None, ids=None, name=None)
"""
scope：作用范围
  function 默认，每个调用的case前/后执行一次
  class
  module 第一次调用的case前执行一次，所有case后执行一次
  session

autouse：
  False 默认，使用fixture需要传参
  True 无需传参

params：可以用list指定哪些case使用fixture
ids：对应params的id
name：fixture的名称，默认就是函数名
"""
# 为区别于用例，函数命名不能以test开头
# 引用方式：把函数名当作用例函数的参数传入
def xxx():
  pass
```

## 运行

参数：

- `-h` 帮助
- `-q` 只显示整体测试结果
- `-v` 显示每个函数的执行结果
- `-s` 显示测试函数中`print()`输出
- `-x` 在第一个错误或测试失败时立即退出

### 命令行运行

- 执行当前目录下所有测试文件：`pytest -参数`
- 执行指定目录下所有测试文件：`pytest -参数 /test_dir`
- 执行指定测试文件：`pytest test_xx.py`
- 执行指定测试函数：`pytest test_xx.py::test_01`
- 执行匹配测试函数：`pytest -k 模糊匹配字段 test_xx.py`

### main方法中运行

```python
# 需要导入pytest
import pytest

if __name__ == '__main__':
    pytest.main(["-参数", "test_xx.py"])
```

## 执行结果：

`.F.F..`中的`.`代表测试通过，`F`（Fail）代表测试失败

- fixture中的断言如果失败，结果会显示error
- case中的断言如果失败，结果会显示failed

### 生成报告文档

不加--self-contained-html时css样式是独立的
pytest --html=report.html --self-contained-html

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

## 参数化

```python
_list = [1, 2, 3]

# 遍历_list中并赋值给x，有几个就会执行几次测试
@pytest.mark.parametrize("x", _list)
def test_01(x):
  print(x)
```