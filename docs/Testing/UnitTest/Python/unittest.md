# UnitTest

```python
import unittest


# 编写单元测试时，我们需要编写一个测试类，从unittest.TestCase继承
class TestMethod(unittest.TestCase):
    #以@开头的为类的方法
    @classmethod
    #所有方法执行前执行一次
    def setUpClass(cls):
        print('类前')
    
    #所有方法执行后执行一次
    @classmethod
    def tearDownClass(cls):
        print('类后')

    #每个方法执行前都要执行一次
    def setUp(self):
        print('test-->setup')

    #每次方法结束后都要执行一次
    def tearDown(self):
        print('test---->tearDown')

    #测试方法，必须以“test”开头
    def test_case01(self):
        print('这是第一个测试方法')
        self.assertEqual(xxx)  # 断言

    def test_case02(self):
        print('这是第二个测试方法')
        self.assertTrue(xxx)  # 断言


if __name__ == '__main__':
    unittest.main()
    """
    用例的执行顺序根据用例名的ASCII码排序执行
    """
```

## Mock

Mock 在 Python3.3 版本已经被集成到了 UnitTest 框架中，直接导入使用即可：`from unittest import mock`
