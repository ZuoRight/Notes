# 引言

## 错误处理

```python
try:
    pass  # 可能会出错的地方
except Xxx1Error as e1:
    print(e1)
except Xxx2Error as e2:
    print(e2)
except (Xxx3Error,Xxx4Error) as es:
    print("可以同时捕获多个异常")
except Exception as e:
    print('通用异常，可匹配任意异常')
except:
    print("如果不写异常名称，将匹配所有异常")
else:
    print("如果没有异常则执行")
finally:
    print("不管有没有异常最后都会执行")
```

## raise 抛出异常

## assert 断言

```python
assert n != 0, "n is zero"  # n如果等于0则抛出次错误提示

assert A and B and C  # 会从做到有断言，只要断言失败，就不会再继续向右继续断言
# 但是更建议分开写
```
