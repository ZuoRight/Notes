# 数组与链表

## array

将「相同」类型的元素存储在「连续」的内存空间中，初始化后数组的长度就固定了，如果数组分配的大小超过实际所需，那么多余的空间就被浪费了，而如果要扩容数组，则需重新建立一个更大的数组，然后把原数组元素依次复制到新数组。

索引本质上是内存地址的偏移量，首个元素偏移0，所以索引从0开始

初始化数组

```python
# 无初始值：大多数编程语言默认初始化为0
nums: list[int] = []

# 有初始值
nums: list[int] = [1, 3, 2, 5, 4]
```

元素存储在连续的空间意味着只要知道数组内存地址（即首元素地址）和元素索引，即可计算出计算元素的内存地址

![20240329205920](https://image.zuoright.com/20240329205920.png)

随机访问的复杂度是 $O(1)$，但查找的复杂度并不是。

```python
# 随机访问元素
def random_access(nums: list[int]) -> int:
    # 在区间 [0, len(nums)-1] 中随机抽取一个数字
    random_index = random.randint(0, len(nums) - 1)
    # 获取并返回随机元素
    random_num = nums[random_index]
    return random_num


# 遍历数组
def traverse(nums: list[int]):
    count = 0
    # 通过索引遍历数组
    for i in range(len(nums)):
        count += nums[i]

    # 直接遍历数组元素
    for num in nums:
        count += num
        
    # 同时遍历数据索引和元素
    for i, num in enumerate(nums):
        count += nums[i]
        count += num


# 在数组的索引 index 处插入元素 num
def insert(nums: list[int], num: int, index: int):
    # 把索引 index 以及之后的所有元素向后移动一位
    for i in range(len(nums) - 1, index, -1):
        nums[i] = nums[i - 1]
    # 将 num 赋给 index 处的元素
    nums[index] = num


# 删除索引 index 处的元素
def remove(nums: list[int], index: int):
    # 把索引 index 之后的所有元素向前移动一位
    for i in range(index, len(nums) - 1):
        nums[i] = nums[i + 1]


# 查找元素
def find(nums: list[int], target: int) -> int:
    for i in range(len(nums)):
        if nums[i] == target:
            return i
    return -1
```

## dynamic array

动态数组，通常也叫列表

本质上是在静态数组的基础上增加了自动扩容的机制。这个扩容机制允许数组在原有空间不足以存储更多元素时自动增加容量，从而提供了比静态数组更灵活的数据存储方式。

许多编程语言中的标准库提供的列表是基于动态数组实现的，比如 Python 的 List

扩展数组长度，其实就是初始化一个更长的新数组，然后将旧数组复制到新数组。

```python
def extend(nums: list[int], enlarge: int) -> list[int]:
    # 初始化一个扩展长度后的数组
    res = [0] * (len(nums) + enlarge)
    # 将原数组中的所有元素复制到新数组
    for i in range(len(nums)):
        res[i] = nums[i]
    # 返回扩展后的新数组
    return res
```

## linkedlist

链表要额外存储指针，相比数组更占内存，但利用率更高，因为连续空间如果不足，数组就需要扩容

![20240329205754](https://image.zuoright.com/20240329205754.png)

```python
# 链表节点类
class ListNode:
    def __init__(self, val: int):
        self.val: int = val  # 节点值
        self.next: ListNode | None = None  # 指向下一节点的引用


# 初始化链表 1 -> 3 -> 2 -> 5 -> 4
# 初始化各个节点
n0 = ListNode(1)
n1 = ListNode(3)
n2 = ListNode(2)
n3 = ListNode(5)
n4 = ListNode(4)
# 构建节点之间的引用
n0.next = n1
n1.next = n2
n2.next = n3
n3.next = n4
```

```python
# 访问节点，需要从头节点开始逐个向后遍历，直到目标节点
def access(head: ListNode, index: int) -> ListNode | None:
    for _ in range(index):
        if not head:
            return None
        head = head.next
    return head

# 插入P节点：n0 -> P -> n1
def insert(n0: ListNode, P: ListNode):
    n1 = n0.next
    P.next = n1
    n0.next = P

# 删除P节点：n0 -> n1，删除后P仍指向n1，但已无法被访问到
def remove(n0: ListNode):
    if not n0.next:
        return
    P = n0.next
    n1 = P.next
    n0.next = n1

# 查找节点
def find(head: ListNode, target: int) -> int:
    index = 0
    while head:
        if head.val == target:
            return index
        head = head.next
        index += 1
    return -1
```
