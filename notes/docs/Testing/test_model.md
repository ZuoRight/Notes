# 测试策略

> <https://time.geekbang.org/column/article/11462>

- 传统软件：慢，通常采用金字塔模型，重点在 GUI 测试

![20230721231203](https://image.zuoright.com/20230721231203.png)

- 互联网软件：快，通常采用菱形模型，重点在 API 测试

保证测试质量和测试覆盖率的前提下，有效缩短测试执行时间

![20230721231626](https://image.zuoright.com/20230721231626.png)

## 测试模型

### V 模型

在编码之后，针对项目的每个阶段都进行测试

![20240522114841](https://image.zuoright.com/20240522114841.png)

### W 模型

从前期文档便开始，尽可能早的参与测试。

![20240522164104](https://image.zuoright.com/20240522164104.png)

### X 模型

针对单独程序片段进行测试，另外还增加了探索性测试

![20240522164413](https://image.zuoright.com/20240522164413.png)

### H 模型

测试是一个独立的过程，达到准入条件便可以开始测试

![20240522164426](https://image.zuoright.com/20240522164426.png)

## 测试驱动开发

- <https://teobler.com/posts/20220131-from-tdd-to-test-strategy>
- <https://time.geekbang.org/column/article/416742>
- <https://time.geekbang.org/column/article/78104>
- <https://time.geekbang.org/column/article/78507>
- <https://time.geekbang.org/column/article/417462>

![20240521184149](https://image.zuoright.com/20240521184149.png)

### TDD

TDD(Test Driven Development)，测试驱动开发，一个经常被提及但很少被执行的开发模式，先编写测试，然后编写能够使测试通过的代码。

也有人认为 TDD 是 Test-Driven Design，先编写测试，驱动你设计出更易与测试的代码，强迫你写出低耦合的代码。

具体由谁来编写测试代码呢？如果是 RD 来写，则他们会以技术思维来写，而且面对描述不清的需求文档，并不能精确的知道每一处细节，另外很多程序员连单元测试都懒得写，更何况在编写代码之前先写好测试用例了，而如果由 PM 来写，则大部分业务方并不具备编码能力，即使懂技术，也可能与 RD 使用的技术栈不一致。

![20240514133301](https://image.zuoright.com/20240514133301.png)

### BDD

于是便催生出一些工具和方法论来解决这些问题，比如 JBehave、Cucumber 等。

2003 年Dan North 重新定义了 TDD，提出了 [BDD](https://dannorth.net/introducing-bdd)(Behavior Driven Development)，行为驱动开发，让业务方可以不写测试，但是希望他们能够以一种形式化的、基于场景的语言（比如 Given-When-Then）来详细描述他们的系统，这样的语言对程序员编写测试也有巨大的价值。

![20240521182742](https://image.zuoright.com/20240521182742.png)

e.g.

- `Given` the user has entered valid login credentials
- `When` a user clicks on the login button
- `Then` display the successful validation message

然而最适合的其实还是 QA，他们是既了解业务又拥有很强技术的人。能够站在业务和用户的角度找出如何破坏系统的方法，同时他们还能了解程序员的思路，戳穿他们的偷工减料。

### ATDD

ATDD(Acceptance Test Driven Development) 验收测试驱动开发

![20240521182811](https://image.zuoright.com/20240521182811.png)

### DDD

DDD(Domain Drive Design) 领域驱动开发
