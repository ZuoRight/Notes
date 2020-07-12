# 字典dict

本质上是一个哈希表

索引：

1. d["k1"]
2. d,get("k1", "null")  #如果不存在返回默认值“null”

## 集合set，{s1, s2, s3}

特点：无序

不支持索引

判断一个元素在不在字典/集合内可以使用：value in dict/set

排序：

1. tuple/list/set：都可以直接用sorted(tuple/list/set) 排序即可
2. dict排序：
   1. 按key排：sorted(d.items(), key=lambda x: x[0])
   2. 按value排：sorted(d.items(), key=lambda x: x[1])

排序后会以列表的形式返回


判断某年是什么生肖：year % 12