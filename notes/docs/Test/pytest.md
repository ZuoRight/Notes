# pytest里固定的一些文件

_init_.py 识别该文件夹为python的package包
conftest.py 一些fixture配置
pytest.ini pytest的主配置文件，可以改变pytest的默认行为
tox.ini 与pytest.ini类似，用tox工具时候才有用
setup.cfg 也是ini格式文件，影响setup.py的行为

- 测试文件，以`test_`开头（以_test结尾也可以）
- 测试类，以`Test`开头，并且不能带有 __init__ 方法
- 测试函数，以`test_`开头
- 断言，使用assert

---

# Case函数

在类外(按优先级)：

- setup_module/teardown_module 所有Case前/后执行一次
  - 优先级最高
  - 自动化中一般可用来启动/关闭浏览器
- setup_function/teardown_function 每个Case前/后执行一次

在类中(按优先级)：

- setup_class/teardown_class 所有Case前/后执行一次
- setup_method/teardown_method 每个Case前/后执行一次
- setup/teardown 每个Case前/后执行一次

优先级越高，setup_越前置，teardown_越后置

# Fixture

fixture与Case函数区别就是多了一个装饰器，不需要以test_开头，Case中直接调用函数名
@pytest.fixture(scope="function", autouse=False, params=None, ids=None, name=None)

- scope 作用范围
  - function 默认，每个调用的Case前/后执行一次
  - class
  - module 第一次调用的Case前执行一次，所有Case后执行一次
  - session

- autouse
  - False 默认，使用fixture需要传参
  - True 无需传参，默认根据scope类型使用

- params 可以用list指定哪些Case使用fixture

- ids 与params对应的id

- name fixture的名称

fixture函数需要返回值，默认为None，可以反回一个元组列表字典等

调用fixture三种方法
1.函数或类里面方法直接传fixture的函数参数名称
2.使用装饰器@pytest.mark.usefixtures()修饰
3.autouse=True自动使用

可同时定义多个fixture，多个之间可以互相调用

fixture中的断言如果失败，结果会显示error
Case中的断言如果失败，结果会显示failed

# 运行单元测试

1. python3 -m pytest test_xx.py

2. 在代码中执行

```python
if __name__ == '__main__':
    pytest.main(["-参数", "test_xx.py"])
```

3. 执行某个目录下所有的Case

pytest 文件名/

4. 执行某一个py文件下Case

pytest .py文件

5. -k 按关键字匹配

pytest -k "MyClass and not method"
将运行TestMyClass.test_something但不运行TestMyClass.test_method_simple

---

pytest -q 简化信息
pytest -s 显示过程

运行结果：.F.F.. 中的.是代表测试通过，F是Fail的意思

---

生成报告文档，不加--self-contained-html时css样式是独立的
pytest --html=report.html --self-contained-html