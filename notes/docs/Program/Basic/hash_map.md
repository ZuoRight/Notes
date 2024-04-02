# 哈希表

![20240401144213](https://image.zuoright.com/20240401144213.png)

基于数组实现的哈希表

数组中的每一个空位被称之为桶（bucket），可以存储一个键值对

```python
class Pair:
    def __init__(self, key: int, val: str):
        self.key = key
        self.val = val
```

通过哈希函数计算出key值对应的数组索引，即桶的位置

```python
class ArrayHashMap:
    def __init__(self):
        """构造方法"""
        # 初始化数组，包含 100 个桶
        self.buckets: list[Pair | None] = [None] * 100

    def hash_func(self, key: int) -> int:
        """哈希函数"""
        index = key % 100
        return index

    def get(self, key: int) -> str:
        """查询操作"""
        index: int = self.hash_func(key)
        pair: Pair = self.buckets[index]
        if pair is None:
            return None
        return pair.val

    def put(self, key: int, val: str):
        """添加操作"""
        pair = Pair(key, val)
        index: int = self.hash_func(key)
        self.buckets[index] = pair

    def remove(self, key: int):
        """删除操作"""
        index: int = self.hash_func(key)
        # 置为 None ，代表删除
        self.buckets[index] = None
```

两个不同的 key 可能在经过哈希函数后得到相同的数组索引，导致查询结果出错，则称之为哈希冲突（hash collision），为了减少哈希冲突的发生，除了扩容之外，需要提高哈希算法的均匀分布性，但理论上是不可避免的，为保证发生哈希冲突时也可以正常工作，有两种处理方式，链式地址和开放寻址

Java 和 Go 都采用了链式地址的方式

![20240401150421](https://image.zuoright.com/20240401150421.png)

Python 采用了开放寻址的方式

![20240401150432](https://image.zuoright.com/20240401150432.png)
