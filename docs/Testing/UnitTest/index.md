# 单元测试

单元测试（Unit Testing）又称为模块测试

将程序分解成独立的可测试的模块，一般是函数、方法、类等代码组织单元，然后对这些单元模块进行正确性检验的测试。

良好的单元测试可以视为一种文档，显示函数或方法应该如何使用，以及在给定输入时应该返回什么。

## 原则

3A + 清理

- 准备（Arrange），初始化测试对象，准备测试数据
- 执行（Act），调用被测方法
- 断言（Assert）
- 清理（Clear）

## 覆盖率

代码覆盖率是度量测试是否全面的指标之一

但不要过分关注测试覆盖率，单元测试不应该包罗万象，不同粒度的测试应该关注不同的点，它们之间是互补的关系

## 插桩

插桩是一种在软件代码中插入额外代码的技术，用于监控和测量程序的行为、性能、资源使用情况等。这些额外代码被称为”桩”（probes）。

- 代码覆盖率分析：插入代码以跟踪哪些代码路径被执行，帮助评估测试覆盖率。
- 性能分析工具：如 Profiler 工具，通过插桩收集性能数据。
- 运行时监控：监控系统或应用程序在运行时的状态和行为。

```python
import time

def some_function():
    print("Executing some_function")

# 插桩代码
def instrumented_function():
    start_time = time.time()
    some_function()
    end_time = time.time()
    print(f"Execution time: {end_time - start_time} seconds")

instrumented_function()
```

## 框架

- Java：`JUnit`、`TestNG`
- JS：`Mocha + Chai`
- Python：`Unittest`、`Pytest`

![20240623235040](https://image.zuoright.com/20240623235040.png)
