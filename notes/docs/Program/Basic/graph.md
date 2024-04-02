# 图

图（Graph）由顶点（vertex）和边（edge）组成。

当两个顶点由边直接连接时，称这两个顶点领接（adjacency）。

某个顶点到另一顶点之间可形成一条路径（path）。

一个顶点拥有的边数称为度（degree），在有向图中还可分为出度和入度，即有多少边从该顶点指出和指向这个顶点。

## 分类

根据边是否有方向可以分为

- 无向图（undirected graph）：顶点之间表示双向，比如好友关系
- 有向图（directed graph）：具有指向性，比如关注与被关注关系

![20240402152002](https://image.zuoright.com/20240402152002.png)

根据所有顶点是否都连通，可分为

- 连通图（connected graph）
- 非连通图（disconnected graph）

![20240402152357](https://image.zuoright.com/20240402152357.png)

根据边是否有权重可分为

- 无权图（unweighted graph）
- 有权图（weighted graph）：比如好友亲密度

![20240402152555](https://image.zuoright.com/20240402152555.png)

## 表示

### 邻接矩阵

使用 `n x n` 矩阵来表示图，`n` 为顶点的数量

`M[i,j] = 1` 或 `0` 表示顶点 `V[i]` 到 `V[j]` 之间存在或不存在边

将邻接矩阵的元素替换为权重，则可表示有权图

![20240402160541](https://image.zuoright.com/20240402160541.png)

### 邻接表

使用 `n` 个链表来表示图

![20240402160600](https://image.zuoright.com/20240402160600.png)
