# 调试

## Pdb

在想要调试的地方前面加一个 `breakpoint()`，然后正常运行程序，执行到断点处便可以进入到 Pdb 调试模式

Pdb 命令行中可以运行任何 Python 命令，检视程序的同时，还可以改变程序状态，比如改变变量值

```python
def demo(data):
    return data * data

def f(x):
    breakpoint()  # 断点
    lst = []
    for i in range(x):
        val = demo(i)
        lst.append(val)
    return lst

f(3)
```

> 参考：<https://www.bilibili.com/video/BV1La4y1T7Y5>

```python
p x  # print(x) 打印，任何Python表达式都可以

l  # 查看当前行前后11行的代码，继续l会往下翻
l .  # 回到当前行
ll  # longlist 可显示当前函数的全部代码

w  # where 查看调用栈，左边箭头标识当前所在帧/frame
u  # up 进入上一帧
d  # down 进入下一帧

n  # next 执行下一行代码，如果有函数调用，调用完进入下一行
s  # step 执行下一行代码，如果有函数调用，进入调用函数
r  # 运行完当前函数，可以用 retval 拿到返回值
c  # continue 继续运行到下一个断点处

until  # 运行直到函数比我当前函数大为止，比如将循环运行完跳出循环
until(7)  # 直接运行到第7行

q  # 退出调试
```

也可以不在代码中加断点，直接执行：`python -m pdb demo.py`，会在第一行代码停下来

```python
b  # break 列出所有断点
b 5  # 在第5行设置breakpoint
b demo  # 在demo函数中设置断点，运行到此处直接进入断点函数中

clear  # 清除所有断点
clear 1  # 清除指定断点
```

## 代码分析工具 Linter

![20240309001317](https://image.zuoright.com/20240309001317.png)

与其它工具都是由 Python 编写不同，Ruff 是基于 Rust 的，支持 VSCode 插件

```shell
pip install ruff

ruff check .                        # 分析当前及子目录内的所有文件
ruff check path/to/code/            # 分析指定目录及子目录内的所有文件
ruff check path/to/code/*.py        # 分析指定目录内的所有py文件
ruff check path/to/code/to/file.py  # 分析 file.py
```

## 类型检查工具

- Mypy
- Pytype
- Pyright：VSCode的插件Pylance便基于此
- Pyre
