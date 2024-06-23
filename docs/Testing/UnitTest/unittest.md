# UnitTest

<https://docs.python.org/3.10/library/unittest.html>

测试对象：加减法计算器

```python
class Calculator:
    def add(self, a, b):
        return a + b

    def subtract(self, a, b):
        return a - b
```

测试用例

```python
import unittest

class TestCalculator(unittest.TestCase):
    def setUp(self):
        """在每个测试函数执行前都会执行此函数，用于设置测试环境"""
        self.calc = Calculator()

    def tearDown(self):
        """在每个测试函数执行后都会执行此函数，用于清理测试环境"""
        del self.calc

    def test_add(self):
        """测试加法功能"""
        self.assertEqual(self.calc.add(1, 2), 3)
        self.assertEqual(self.calc.add(-1, 1), 0)
        self.assertEqual(self.calc.add(-1, -1), -2)

    def test_subtract(self):
        """测试减法功能"""
        self.assertEqual(self.calc.subtract(10, 5), 5)
        self.assertEqual(self.calc.subtract(-1, -1), 0)
        self.assertEqual(self.calc.subtract(-1, 1), -2)
    

if __name__ == '__main__':
    unittest.main()
```

```python
import unittest


# 编写单元测试时，我们需要编写一个测试类，继承自 unittest.TestCase
class TestMethod(unittest.TestCase):
    # 所有方法执行前执行一次
    @classmethod
    def setUpClass(cls):
        print('类前')
    
    # 所有方法执行后执行一次
    @classmethod
    def tearDownClass(cls):
        print('类后')

    # 每个方法执行前都要执行一次
    def setUp(self):
        print('test-->setup')

    # 每个方法结束后都要执行一次
    def tearDown(self):
        print('test---->tearDown')

    # 测试方法，必须以 test 开头
    def test_case01(self):
        print('这是第一个测试方法')
        self.assertEqual(xxx)  # 断言

    def test_case02(self):
        print('这是第二个测试方法')
        self.assertTrue(xxx)  # 断言


if __name__ == '__main__':
    unittest.main()
```

## 参数化

### subTest

```python
import unittest

class TestCalculator(unittest.TestCase):
    def setUp(self):
        self.calc = Calculator()

    # 虽然有3条数据，实际还是一条Case
    # 不过失败的数据不会中断测试，而且会打印出每条错误的信息
    def test_add(self):
        test_cases = [(1, 2, 3), (-1, 1, 2), (-1, -1, 2)]
        for a, b, expected in test_cases:
            with self.subTest(a=a, b=b, expected=expected):
                self.assertEqual(self.calc.add(a, b), expected)

if __name__ == '__main__':
    unittest.main()
```

### parameterized

`pip install parameterized`

```python
import unittest
from parameterized import parameterized

class TestCalculator(unittest.TestCase):
    # 会视为3条独立的用例执行
    test_cases = [(1, 2, 3), (-1, 1, 0), (-1, -1, -2)]
    @parameterized.expand(test_cases)
    def test_add(self, a, b, expected):
        calc = Calculator()
        self.assertEqual(calc.add(a, b), expected)

if __name__ == '__main__':
    unittest.main()
```

## 断言

除了下面这些，还有很多其它的断言方法

<https://docs.python.org/3.10/library/unittest.html#assert-methods>

![20240622222725](https://image.zuoright.com/20240622222725.png)

## 执行

用例的执行顺序根据用例名的ASCII码排序执行

```shell
python -m unittest test_module1 test_module2
python -m unittest test_module.TestClass
python -m unittest test_module.TestClass.test_method
```

## HTMLTestRunner

专用于 unittest 生成测试报告的扩展库

`pip install html-testRunner`

<https://github.com/oldani/HtmlTestRunner>

```python
import unittest
import HtmlTestRunner

class TestStringMethods(unittest.TestCase):
    pass


if __name__ == '__main__':
    unittest.main(testRunner=HtmlTestRunner.HTMLTestRunner(output='example_dir'))
```
