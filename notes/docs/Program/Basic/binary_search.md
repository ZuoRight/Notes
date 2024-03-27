# 二分查找

二分查找是将【有序】【数组】从中点索引处分为两部分，然后根据目标值与中间元素值比较结果，决定排除哪一半区间，并在剩余区间执行相同的二分操作。

适用场景：有序、数组。若数据无序而为了使用二分查找先排序，则光排序的时间复杂度就比暴力搜索要高，得不偿失。另外二分查找需要跳跃式的访问元素，所以基于链表等非连续内存存储的数据结构效率较低。

## 示例1

> 给定一个长度为 n 的数组 nums ，元素按从小到大的顺序排列且不重复。请查找并返回元素 target 在该数组中的索引。若数组不包含该元素，则返回 -1。

初始化指针 `start` 和 `end` 分别指向数组首元素和尾元素的索引，中间索引用 `mid` 表示，即 `mid = (start+end)//2`（向下取整） 为了避免计算 `start+end` 超过取值范围，通常使用公式 `start+(end-start)//2` 来计算，不过 Python 的数字可以无限大（取决于内存大小），一般不需要考虑越界问题。

![20240325145419](https://image.zuoright.com/20240325145419.png)

有两种区间表示法，通常推荐使用双闭区间表示，相对不易出错

```python
def binary_search(nums, target):
    start, end = 0, len(nums) - 1
    while start <= end:
        mid = (start + end) // 2
        if target > nums[mid]:
            start = mid + 1
        elif target < nums[mid]:
            end = mid - 1
        else:
            return mid
    return -1
```

在二分循环中，区间每轮缩小一半，因此循环次数为 $log_2n$，所以时间复杂度为 $O(logn)$，空间复杂度为 $O(1)$

## 示例2

二分查找还可以用于解决许多变种问题，比如在插入排序算法中，为了提高查找插入点的效率，可以考虑使用二分查找来搜索插入点。

> 给定一个长度为 n 的数组 nums ，元素按从小到大的顺序排列。将元素 target 插入数组并保持有序性。若数组中已存在该元素，则插入到其左侧，返回插入后 target 的索引。

- 不存在重复元素

```python
def binary_search_insersion(nums, target):
    start, end = 0, len(nums) - 1
    while start <= end:
        mid = (start + end) // 2
        if target > nums[mid]:
            start = mid + 1
        elif target < nums[mid]:
            end = mid - 1
        else:
            # 说明数组中包含target，插入其左侧，则插入点其实就是target的索引
            return mid
    # 没有找到target，说明不包含，插入点就是最终i所在的索引
    return start  # 只有这里返回与普通二分查找不一样
```

- 存在重复元素

```python
def binary_search_insersion(nums, target):
    start, end = 0, len(nums) - 1
    while start <= end:
        mid = (start + end) // 2
        if target > nums[mid]:
            start = mid + 1
        elif target < nums[mid]:
            end = mid - 1
        else:
            # 找到目标后，由于不确定是否重复，需要继续向左缩小范围，直到找不到为止，可以和target<中间值的情况合并，不过分开写逻辑更清晰
            end = mid - 1
    return start
```
