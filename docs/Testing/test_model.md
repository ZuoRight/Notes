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

## 测试左移

Shift-Left Testing，意味着将测试提前到开发过程的早期阶段，尽早发现问题，减少功能测试时的压力

- 单元测试
- 集成测试，持续集成
- 代码静态分析
- 代码审查（Code Review）

## 测试右移

Shift-Right Testing，强调在部署后进行测试，在真实的运行环境中对软件进行评估，以便捕获并解决那些在标准测试环境可能未被发现的问题，确保软件在生产环境中的稳定性和可靠性

- 监控，生产数据、关键指标、短信邮件等
- 日志分析
- 性能测试
- 用户反馈
- AB 测试

## 精准化测试

- 根绝代码变更自动分析影响范围
- 代码调用链与黑盒测试用例关联
- 代码流程分析与覆盖率统计
- 黑盒测试过程中借助代码流程覆盖数据指导探索式测试
- 利用线上数据推导有效测试用例

## 混沌工程

混沌工程是一种软件工程方法论，旨在通过有意地在系统中引入故障来测试系统的可靠性和稳定性。

这种做法背后的基本原理是通过模拟可能导致系统故障的各种场景（例如硬件故障、网络延迟、系统资源耗尽等），来验证和提高系统在生产环境下的弹性。

1. 定义稳态：确定系统的正常行为（稳态），以便在引入故障时能够衡量系统偏离这种状态的程度。
2. 变量假设：明确系统设计中的假设，例如，某个服务的高可用性或数据库的快速响应。
3. 小规模开始：从小规模和非核心系统开始引入故障，逐步扩大到更大的系统。
4. 故障注入：使用混沌工具（如 Chaos Monkey、Gremlin 等）在系统中引入各种故障（如关闭服务器、制造网络拥堵或断开数据库连接）。
5. 监控和分析：监控系统的响应并分析故障注入对系统稳态的影响。
6. 优化和修复：根据测试结果进行系统调整和修复，增强系统的弹性。
