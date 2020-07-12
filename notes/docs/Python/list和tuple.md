# 数据结构

列表和元组，都是有序的，都可以放置任意数据类型的元素（其它语言一般不可以），列表类似其他语言的数组（array）。

元组是静态的，不可变（不能增删改查），列表是动态的，均支持索引（还可以负数索引），支持切片，支持任意嵌套。

元组的存储空间固定，相比于列表更节省空间，初始化相同元素时元组要快5倍(linux系统)，但在索引时性能几乎没有差别，所以若要存储固定的数据，优先考虑使用元组。

## 定义

- 空列表/元组

> 优先使用方法1，因为`[]`是一个可直接调用的内置C函数，效率高

```python
# 方法1
empty_list = []
empty_tuple = ()
# 方法2
empty_list = list()
empty_tuple = tuple()
```

- 一个元素的元组

> 方法1一定要加逗号，不然会被认为是字符串

```python
# 方法1
one_tuple = ("a",)
# 方法2
one_tuple = tuple("a")
```

## 列表的增删改

```python
# 增
l.append("x")  # 末位增一个
l.extend(seq)  # 末位增一组(可以是str/list/tuple/dict/set)
l.insert(index,"x")  # 指定位置插入

# 删
l.pop()  # 删除末位
l.pop(index)  # 删除指定位
l.remove("x")  # 删除指定元素（如有重复，删除第一个）
del l[m:n]  # 删除切片

# 改
l[index] = "x"  # 替换指定位置元素
```

## 常用内置函数

```python
l = ["a", "b", "c", "a", "a", "b"]
t = ("a", "b", "c", "a", "a", "b")

# 统计列表/元组中item出现的次数
l.count("a")

# 返回列表/元组中item第一次出现的索引
l.index("b")

# 反转列表，原列表变了
l.reverse()
# 反转后得到一个迭代器，需要再转化一下，原元组/元组没变
new_l = list(reversed(t))
new_t = tuple(reversed(t))

# 列表排序，原列表改变了
l.sort()
print(l)
# 排序后得到新的元组/列表，原列表/元组没变
new_l = sorted(l)
new_t = sorted(t)
# 倒序
l.sort(reverse=True)
new_l = sorted(l, reverse=True)

# zip
```
