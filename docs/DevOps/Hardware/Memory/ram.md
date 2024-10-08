# 内存

内存其实包括主存和缓存，实际使用中通常指的是主存 RAM

当进程被创建时，操作系统为它分配一块虚拟内存空间，是一个连续的地址空间，有起始地址和结束地址

> 最小寻址内存单元为： 1 字节

内存空间中通常有两部分

- 堆内存（Heap）用户主动请求而划分出来的内存区域，从起始地址开始，由内存的低位地址向高位地址增加
- 栈内存（Stack）其它内存区域叫做栈，从结束地址开始，由内存的高位地址向低位地址增加。

> 注意：这里的堆栈与数据结构中的堆和栈不是同一个概念，不过内存中的栈确实就是一种栈结构，而堆则类似链表。

![20221118122912](http://image.zuoright.com/20221118122912.png)

C 语言中全局变量存在堆，函数运行结束后需要手动释放，否则会内存泄漏(memory leak)，局部变量存在栈，由系统管理，函数运行时进入内存，运行结束时自动清理。

虚拟内存空间又可分为

- 系统内核程序使用的内存空间
- 进程使用的内存空间

对于进程来说，它像是在一个独立且完整的内存中运行，这个机制通过硬件支持的内存管理单元（MMU）和操作系统的协作实现。

虚拟内存地址并不直接对应物理内存地址。当进程访问其虚拟地址空间中的数据时，MMU 根据当前的页面映射（由操作系统维护）将虚拟地址转换为物理地址。

如果所需数据不在物理内存中（缺页），则会触发缺页中断，操作系统负责从磁盘加载数据到物理内存，并更新页面映射。

- 查看内存使用情况

```shell
# 静态，数据大小单位默认KB，-m选项用M为单位
free -m

# 动态
top
```