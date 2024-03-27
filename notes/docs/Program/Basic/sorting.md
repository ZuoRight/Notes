# 排序

将一组数据按照特定顺序进行排列，比如数字大小、ASCII 码顺序或自定义规则。

排序算法的几个评价维度

- 运行效率，即时间复杂度
- 稳定性，完成排序后，重复元素的相对顺序不发生改变
- 就地性，可在原数组上直接操作，而无需借助额外的辅助数组，更省内存
- 自适应性，受输入数据影响以至于平均/最佳/最差复杂度不同，反之为非自适应，如果最佳优于平均，则属于正面的自适应
- 基于比较，需要依赖比较运算符(>, =, <)则更通用，若不依赖则更高效

![20240325201120](https://image.zuoright.com/20240325201120.png)

对于短数组，通常使用插入排序。对于长数组，采用基于分治策略的排序算法。比如 Python 底层使用的 `Timsort` 算法，结合使用了插入排序和归并排序。

排序算法至少需要两个循环

## 遍历排序 $O(n^2)$

虽然三种遍历排序的平均时间复杂度相同，但插入排序比选择排序更稳定，且遇到有序数组时最佳时间复杂度更快，而冒泡排序由于需要交换元素需要借助临时变量，因此需要更多的操作单元，所以插入排序实际使用频率相对更高。

于是用移动操作来替代交换操作，可以提高处理速度

### selection sort

选择排序，开启一个双循环，每轮从未排序区间选择最小的元素，将其放到已排序区间的末尾

```python
def selection_sort(nums):
    n = len(nums)
    # 外循环
    for i in range(n - 1):
        # 内循环：找到未排序区间内的最小元素
        k = i
        for j in range(i + 1, n):
            if nums[j] < nums[k]:
                k = j  # 记录最小元素的索引
        # 将该最小元素与未排序区间的首个元素交换
        nums[i], nums[k] = nums[k], nums[i]
```

### bubble sort

冒泡排序，通过连续地比较与交换相邻元素实现排序。这个过程就像气泡从底部升到顶部一样。

```python
def bubble_sort(nums):
    # 外循环：未排序区间为 [0, i]
    for i in range(len(nums) - 1, 0, -1):
        flag = True  # 初始化标志位
        # 内循环：将未排序区间 [0, i] 中的最大元素交换至该区间的最右端
        for j in range(i):
            if nums[j] > nums[j + 1]:
                # 交换 nums[j] 与 nums[j + 1]
                nums[j], nums[j + 1] = nums[j + 1], nums[j]
                flag = False
        # 此轮冒泡flag没有变为过False，即未交换任何元素，则说明数组已经完成排序，可直接跳出
        if flag:
            break
```

### insertion sort

插入排序，在未排序区间选择一个基准元素 base，将该元素与其左侧已排序区间的元素逐一比较大小，并将该元素插入到正确的位置。类似整理一手扑克牌的过程。

```python
def insertion_sort(nums):
    # 外循环
    for i in range(1, len(nums)):
        base = nums[i]
        end = i - 1  # 已排序区间为 [0, i-1]
        # 内循环：将插入点后的元素依次向后移动一位
        while end >= 0 and base < nums[end]:
            nums[end + 1] = nums[end]
            end -= 1
        # 将 base 插入到已排序区间 [0, i-1] 中的正确位置
        nums[end + 1] = base
```

二分插入排序，将基准元素插入到前面已经排序好的子数组中时，使用二分查找来更快地找到插入的位置，从而减少比较的次数。

```python
def binary_search_insertion(nums, target, end):
    # 需要传入尾元素的索引
    start, end = 0, end
    while start <= end:
        mid = (start + end) // 2
        if target > nums[mid]:
            start = mid + 1
        else:
            end = mid - 1
    return start  # 插入点

def binary_insertion_sort(nums):
    # 外循环
    for i in range(1, len(nums)):
        base = nums[i]
        end = i - 1  # 已排序区间为 [0, i-1]
        # 内循环：使用二分查找提高搜索插入点的效率
        pos = binary_search_insertion(nums, base, end)
        # 将插入点后的元素从大到小依次向后移动一位
        for j in range(i, pos, -1):
            nums[j] = nums[j - 1]
        # 将基准元素插入到插入点
        nums[pos] = base
```

## 分治排序 $O(nlogn)$

虽然三种分治排序平均时间复杂度相同，但通常快速排序效率更高，因为其比较、赋值、交换等操作的总数量最少，只是在概率较小的最差情况下没有归并排序稳定。相比于堆排序不需要跳跃式访问元素，所以对缓存的使用效率更高。

对于链表，归并排序相较于其他排序算法具有显著优势

### quick sort

快速排序，其实就是将一个较长的数组简化为两个较短的数组排序，选择数组中的某个元素作为基准数，将所有小于基准数的元素移到其左侧，而大于基准数的元素移到其右侧，原数组被划分成三部分：左子数组、基准数、右子数组，这个过程叫做哨兵划分，然后分别将左右子数组递归继续哨兵划分，直到数组长度变为1时终止。

```python
def quick_sort(nums, left, right):
    # 子数组长度为1时终止递归
    if left >= right:
        return
    # 哨兵划分
    pivot = partition(nums, left, right)
    # 分别递归左、右子数组
    quick_sort(nums, left, pivot - 1)
    quick_sort(nums, pivot + 1, right)


def partition(nums, left, right):
    # 以 nums[left] 为基准数
    i, j = left, right
    while i < j:
        # 首先从右向左找首个小于基准数的元素
        while i < j and nums[j] >= nums[left]:
            j -= 1
        # 然后从左向右找首个大于基准数的元素
        while i < j and nums[i] <= nums[left]:
            i += 1
        # 交换元素位置
        nums[i], nums[j] = nums[j], nums[i]
    # 将基准数与子数组分界线处(即指针i处)的元素交换
    nums[i], nums[left] = nums[left], nums[i]
    return i  # 返回基准数的索引
```

- 哨兵划分的查找顺序

在哨兵划分中，当我们以最左端元素为基准数时，必须先【从右往左查找】再从左往右查找。

假设数组为 `[4, 2, 1, 3, 5]`，基准数为 4，先从左向右找大于 4 的元素，直到最右侧才找到比 4 大的元素，此时 `i` = `j` 跳出循环，交换 `nums[left]` 和 `nums[i]` 后，数组变为 `[5, 2, 1, 3, 4]`，左子数组 `[5, 2, 1, 3]` 中的 5 比基准数要大，显然哨兵划分失败。

- 哨兵划分的基准数优化

假设数组完全倒序，以 left 为基准数则每次递归哨兵划分都会产生一个长度为0的右子数组，导致分治策略无效，为了避免这种情况，可以优化基准数选取策略，比如随机选取基准数，或者选择首、中、尾三个元素中的中位数。

说明：虽然基准数是随机选取的，但选取后要先交换到最左侧，也就是说，仍然是以 `nums[left]` 为基准的

```python
# 选取三个候选元素的中位数
def median_three(nums, left, mid, right):
    l, m, r = nums[left], nums[mid], nums[right]
    if (l <= m <= r) or (r <= m <= l):
        return mid  # m 在 l 和 r 之间
    if (m <= l <= r) or (r <= l <= m):
        return left  # l 在 m 和 r 之间
    return right

# 哨兵划分（三数取中值）
def partition(nums, left, right):
    # 选取中位数为基准数，并交换至数组最左端
    med = median_three(nums, left, (left + right) // 2, right)
    nums[left], nums[med] = nums[med], nums[left]

    # 以 nums[left] 为基准数，不变
    pass
```

### merge sort

归并排序，先递归划分左子数组，再递归划分右子数组，最后处理合并

![20240327230145](https://image.zuoright.com/20240327230145.png)

```python
def merge_sort(nums, left, right):
    # 当子数组长度为 1 时终止递归
    if left >= right:
        return
    # 划分阶段
    mid = (left + right) // 2  # 计算中点
    merge_sort(nums, left, mid)  # 递归左子数组
    merge_sort(nums, mid + 1, right)  # 递归右子数组
    # 合并阶段
    merge(nums, left, mid, right)


# 合并左子数组和右子数组
def merge(nums, left, mid, right):
    # 左子数组区间为 [left, mid], 右子数组区间为 [mid+1, right]
    # 创建一个临时数组 tmp ，用于存放合并后的结果
    tmp = [0] * (right - left + 1)
    # 初始化左子数组和右子数组的起始索引
    i, j, k = left, mid + 1, 0
    # 当左右子数组都还有元素时，进行比较并将较小的元素复制到临时数组中
    while i <= mid and j <= right:
        if nums[i] <= nums[j]:
            tmp[k] = nums[i]
            i += 1
        else:
            tmp[k] = nums[j]
            j += 1
        k += 1
    # 将左子数组和右子数组的剩余元素复制到临时数组中
    while i <= mid:
        tmp[k] = nums[i]
        i += 1
        k += 1
    while j <= right:
        tmp[k] = nums[j]
        j += 1
        k += 1
    # 将临时数组 tmp 中的元素复制回原数组 nums 的对应区间
    for k in range(0, len(tmp)):
        nums[left + k] = tmp[k]
```
