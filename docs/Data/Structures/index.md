# 数据结构

![20240403154117](https://image.zuoright.com/20240403154117.png)

## 根据物理结构分类

物理结构描述了数据在计算机内存中的存储方式，决定了数据的增删改查等操作方法

所有数据结构都是基于「数组」和「链表」，或者其组合实现的，比如，哈希表的底层是数组，其中每一个桶槽位可能包含一个值，但也可能包含一个链表或一棵树。

![20240109000953](https://image.zuoright.com/20240109000953.png)

基于数组实现的数据结构也称静态数据结构，这意味着此类数据结构在初始化后长度不可变。（动态数组或者说列表比较特殊）

相对应地，基于链表实现的数据结构也称动态数据结构，这类数据结构在初始化后，仍可以在程序运行过程中对其长度进行调整。

## 根据逻辑结构分类

逻辑结构描述了数据元素之间的逻辑关系，也叫抽象数据类型（ADT, Abstract data types），只定义他们的抽象视图（数学和逻辑模型），可以用编程语言以多种方式去实现它

- 线性结构

都有一个逻辑起点和逻辑终点

![20210911230633](http://image.zuoright.com/20210911230633.png)

- 非线性结构

![20210911230724](http://image.zuoright.com/20210911230724.png)

## 参考

- [Harsha 讲解的数据结构（B 站转译）](https://www.bilibili.com/video/BV1Fv4y1f7T1)
- [Hello Algo](https://www.hello-algo.com/)
- [演算法笔记](https://web.ntnu.edu.tw/~algo/)
- [Python 数据结构与算法示例](https://github.com/keon/algorithms)
- [数据结构与算法之美](https://time.geekbang.org/column/intro/100017301), [Python 实现](https://github.com/wangzheng0822/algo/tree/master/python)
- [labuladong 的算法小抄](https://github.com/labuladong/fucking-algorithm)
- [Leetcode 力扣](https://leetcode.cn/problemset/)
