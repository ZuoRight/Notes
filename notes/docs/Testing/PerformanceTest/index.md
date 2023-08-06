# 性能测试

> 主要参考了极客时间高楼老师讲的性能测试系列专栏

针对系统的性能指标，建立性能测试模型，制定性能测试方案，制定监控策略，在场景条件之下执行性能场景，分析判断性能瓶颈并调优，最终得出性能结果来评估系统的性能指标是否满足既定值。

给运维做系统容量规划提供依据，最小化成本，给开发提供应用调优参考，满足用户使用需求。

建议不要使用像性能测试、负载测试、容量测试这样的词来概括性能执行策略，这些定义只能片面的表示性能测试的一个点或面，不能完整的表达出来性能测试，并且定义之间的界限非常模糊，对实施过程没有什么指导价值。

![20211125161809](http://image.zuoright.com/20211125161809.png)

项目分类

- 新系统性能测试类：这样的项目一般都会要求测试出系统的最大容量，不然上线心里没底。
- 旧系统新版本性能测试类：这样的项目一般都是和旧版本对比，只要性能不下降就可以根据历史数据推算容量，对调优要求一般都不大。
- 新系统性能测试优化类：这类的系统不仅要测试出最大容量，还要求调优到最好。

职责定位

- 性能验证：针对给定的指标，只做性能验证。第三方测试机构基本上都是这样做的。
- 性能测试：针对给定的系统，做全面的性能测试，可以得到系统最大容量，但不涉及到调优。
- 性能测试 + 分析调优：针对给定的系统，做全面的性能测试，同时将系统调优到最优状态。

## 指标

技术指标

- 时间指标：接口响应时间、业务响应时间
- 容量指标：接口容量、业务容量
- 资源利用率指标：操作系统（CPU、IO、Memory、Disk、Network、System）、JVM（GC、Classes）

技术指标不能脱离业务指标，比如：1000万在线用户

![20211125164557](http://image.zuoright.com/20211125164557.png)

最关键的指标当属TPS，它可以反应出一个系统的处理能力

TPS在不同的行业不同业务中定义的粒度都是不同的，所以用TPS的前提是所有相关的人都要知道你的事务(Transactions)是如何定义的。

## 模型

一些金融业务线上交易不容出错，需要在测试环境中用业务模型来模拟出生产的流量，或者从生产流量中导过来

## 监控

监控要有分层、分段的能力，要有全局监控、定向监控的能力

监控是为了取证，分析是为了调优

## 场景/用例

> 在既定的环境（包括动态扩展等策略）、既定的数据（包括场景执行中的数据变化）、既定的执行策略、既定的监控之下，执行性能脚本，同时观察系统各层级的性能状态参数变化，并实时判断分析场景是否符合预期。

- 基准性能场景：这里要做的是单交易的容量，为混合容量做准备。
- 容量性能场景：这一环节必然是最核心的性能执行部分。根据业务模型复杂度的不同，这部分的场景会设计出很多个。
- 稳定性性能场景：在稳定性测试中，显然最核心的元素是时间，而时间的设置应该来自于运维周期，而不是来自于老板、产品和架构等这些人的心理安全感。
- 异常性能场景：要做异常性能场景，前提就是要有压力。在压力流量之下，模拟异常。

![20220808163611](http://image.zuoright.com/20220808163611.png)

## 示意图

比较符合实际的一张图

- 蓝色：TPS
- 黄色：RT

![20220808153704](http://image.zuoright.com/20220808153704.png)

与实际不太相符的经典示意图

![20211125165501](http://image.zuoright.com/20211125165501.png)

三条曲线

- 利用率（Utilization 绿）
- 吞吐量（Throughput 紫）
- 响应时间（Response Time 蓝）

三个区域

- 轻负载区（Light Load），有人将其对应为性能测试
- 重负载区（Heavy Load），有人将其对应为负载测试
- 塌陷区（Buckle Zone），有人将其对应为压力测试

两个点

- 最优并发用户数（The Optimum Number of Concurrent Users）
- 最大并发用户数（The Maximum Number of Concurrent Users）

三个状态描述

- 资源饱和（Resource Saturated）
- 吞吐下降（Throughput Falling）
- 用户受影响（End Users Effected）

## 报告

至少要有调优前后的TPS、响应时间以及资源对比图

## 工具

推荐：JMeter

![20211125164448](http://image.zuoright.com/20211125164448.png)

## Load Average

> 可使用`uptime`命令查看，结果其实就是`top`命令返回的第一行信息

平均负载，指单位时间内处于可运⾏状态和不可中断状态的进程数，负载过⾼可能会导致进程响应变慢进⽽影响服务的正常功能

- 可运⾏状态的进程(R)：正在使⽤CPU的进程(Running)、正在等待CPU的进程(Runnable)
- 不可中断状态的进程(D)：正在等待I/O的进程(Disk Sleep)，这些进程正在与硬件进⾏不允许中断的交互（系统保护）

### 压测分析

> 平均负载与CPU使用率不一定对应，最理想的就是每个CPU上都刚好运⾏着⼀个进程，这样每个CPU都得到了充分利⽤

- stress

```shell
# 压测工具，可用来模拟各种异常进程
apt install stress

# CPU 密集型进程（此时CPU 使⽤率与LA是一致的）
stress --cpu 1 --timeout 600  # 模拟⼀个 CPU 使⽤率 100% 的场景
# I/O 密集型进程（此时 CPU 使⽤率不一定升高）
stress -i 1 --timeout 600  # 模拟 I/O 压⼒（不停地执⾏ sync）
# ⼤量进程的场景
stress -c 8 --timeout 600  # 模拟8个进程
```

- sysstat

```shell
# 分析工具
apt install sysstat

# mpstat，多核 CPU 性能分析⼯具，⽤来实时查看每个 CPU 的性能指标以及平均指标
mpstat -P ALL 5  # -P ALL 表示监控所有CPU，后面数字5表示间隔5秒后输出一组数据
# pidstat，进程性能分析⼯具，⽤来实时查看进程的 CPU、内存、I/O 以及上下⽂切换等性能指标
pidstat -u 5 1  # 查看每个进程的状态，看是哪个导致了 CPU 使⽤率过载
# iostat
```