# 引言

![20211126111629](http://image.zuoright.com/20211126111629.png)

操作 Excel 2007 文件时（`.xls`），不同操作需要使用不同的库

操作 Excel 2010 文件时（`.xlsx/xlsm/xltx/xltm`），`OpenPyXL` 库可以同时满足常见的读写等操作

数据科学相关

- NumPy 是 Python 的数值计算扩展，专门用来处理矩阵，运算效率比列表更高效
- Scipy 是基于 NumPy 的科学计算包，包括统计、线性代数等工具
- Pandas 是基于 NumPy 的数据分析工具，能更方便的操作大型数据集，支持多种数据格式的读写，如 CSV、Excel、JSON、SQL等，有 Series 和 DataFrame 两种主要的数据结构，支持复杂的多维数据操作。

Tablib 其实就是将操作各种格式的库封装了一层，比如操作 `.xlsx`，底层依赖的依然是 openpyxl 这个库
