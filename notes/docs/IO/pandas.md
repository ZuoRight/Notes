# Pandas

<https://zhuanlan.zhihu.com/p/340770847>

```python
# 数据以 Series 或 DataFrame 格式返回
DataFrame.apply(func, axis=0, raw=False, result_type=None, args=(), **kwds)
"""
func 函数或lambda表达式
axis
  0/index 处理每一列（默认）
  1/columns 处理每一行
raw
  False 把每一行或列作为Series传入函数中
  True 接受的是ndarray数据类型
"""
```

df.loc[2] 返回指定行
