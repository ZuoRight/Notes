# 索引

索引的出现其实就是为了提高数据查询的效率，就像书的目录一样

- 主键索引（聚簇索引）
- 普通索引（二级索引）
- 唯一索引，可以为 NULL

把表中某列的值存储到某种数据结构中，就叫索引

优点

- 提高数据检索效率，降低数据库 IO 成本
- 通过索引对数据进行排序，降低 CPU 消耗

缺点

- 占用空间
- 降低了表更新的速度
- 建立最优索引需要花时间研究

适合建立索引的字段：主键，外键，频繁作为查找或排序的字段

索引的效率取决于索引列的值是否散列，即该列的值如果越互不相同，那么索引效率越高，像性别这种列有索引反而会降低效率，这种情况下大数据中会使用分组的概念

## 索引模型

在 MySQL 中，索引是在存储引擎层实现的，而 MySQL 的存储引擎是插件化的，所以并没有统一的索引标准，不同存储引擎的索引的工作方式并不一样。即使多个存储引擎支持同一种类型的索引，其底层的实现也可能不同。

InnoDB 使用了 B+ 树索引模型，即数据都是存储在 B+ 树中的，每一个索引在 InnoDB 里面对应一棵 B+ 树

主键索引只需要搜索 ID 这颗树

普通索引需要先搜索当前索引的 B+ 树，然后再搜索 ID 索引树（这个过程叫做：回表）

## 性能优化

I/O 是 DBMS 最容易出现瓶颈的地方，可以说数据库操作中有大量的时间都花在了 I/O 上。

慢 SQL 优化，指的是对那些在执行过程中花费较长时间的 SQL 查询进行优化，以提高其执行速度和整体性能

慢 SQL 查询的常见原因

- 缺少索引：查询涉及的表没有适当的索引，导致全表扫描。
- 不合适的索引使用：索引使用不当或者复合索引未能被正确利用。
- 不合理的查询设计：查询语句本身设计不当，如使用了大量的嵌套子查询、未优化的 JOIN 等。
- 数据量过大：查询涉及的数据量过大，没有进行分页或分区。
- 服务器资源不足：数据库服务器的 CPU、内存或磁盘 I/O 资源不足。
- 锁竞争：并发查询之间发生锁竞争，导致查询等待时间增加。

比如对索引字段做函数操作，或有隐式类型转换涉及函数操作，或有隐式字符编码转换，可能会破坏索引值的有序性，因此优化器就决定放弃走树搜索功能，导致全索引扫描。

因此用 `explain + SQL 语句` 分析一下，是一个很好的习惯，它可以模拟优化器执行 SQL 查询语句。

- 尽量只查询所需要的列
- 当你知道只有 1 条记录的时候，就可以使用LIMIT 1来进行约束
