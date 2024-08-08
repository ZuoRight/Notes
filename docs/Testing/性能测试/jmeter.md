# JMeter

[官网](https://jmeter.apache.org){ .md-button .md-button--primary }

![20211124153427](http://image.zuoright.com/20211124153427.png)

解压后可以命令行切换到 `apache-jmeter-5.4.1/bin` 路径下，使用 `bash jmeter` 命令启动图形化界面，也可以命令行的方式直接使用

为了方便，可以配置环境变量在任意路径下打开，以下是 Mac 系统的环境变量配置

```shell
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_311.jdk/Contents/Home
export JMETER_HOME=/Users/7c/apache-jmeter-5.4.1
export PATH=$PATH:$JAVA_HOME/bin:$PATH:.:$JMETER_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JMETER_HOME/lib/ext/ApacheJMeter_core.jar:$JMETER_HOME/lib/jorphan.jar
```

![20211124154556](http://image.zuoright.com/20211124154556.png)

> 切换语言：`Options > Choose Language`

!!! Tips
    如果启动后有闪退或者报错等问题，大概率跟 jdk 版本有关，我在 `jdk-11.0.11` 及以上版本都有遇到过各种问题，然后降低到 `jdk1.8.0_311.jdk` 版本后就稳定多了

## 测试组件

> <https://jmeter.apache.org/usermanual/component_reference.html>

测试计划 [Test Plan](https://jmeter.apache.org/usermanual/test_plan.html) 描述了 Jmeter 在运行时将要执行的一系列步骤，由各种组件构成

- Config Element 各种配置元素，比如 HTTP 请求默认配置、计数器、JDBC 连接配置、CSV 配置等
- Samplers 采样器，添加各种请求
- Logic Controllers 逻辑控制器，控制如何发送请求
- Timers 定时器，比如同步定时器可以阻塞线程，达到一定数量后同时下发
- Pre Processor 预处理器，发出采样请求前执行的操作
- Post Processor 后处理器，发出采样请求后执行的操作，通常用于处理响应数据，比如正则或者解析 JSON 获取下一个请求的依赖值等
- Assertions 断言
- Listeners 侦听器，查看请求响应和断言结果等

还可以通过插件管理器添加其它组件

下载插件管理 Jar 包：<https://jmeter-plugins.org/wiki/PluginsManager/>

将 `jmeter-plugins-manager-1.10.jar` 放入 `apache-jmeter-5.6.2/lib/ext` 路径下

然后在菜单栏 `Options` 选项中即可看到 `Plugins Manager` 入口

![20230821172152](https://image.zuoright.com/20230821172152.png)

## 线程组 Threads

线程组是任何测试计划的起点，所有 Samplers 和 Controllers 组件必须位于线程组下

组件若放在测试计划下则表示应用于所有线程组

![20240808234322](https://image.zuoright.com/20240808234322.png)

### `Number of Threads`

线程数，单位：users

在某些工具中，线程也被称作虚拟用户，所以线程数通常也被称作用户数

但是通常所说的并发不用并发线程数来表示系统的负载能力，而是用 TPS 来表示并发用户数

随着线程数的增加，TPS 也会跟着递增，增加到一定程度后会达到饱和状态

### `Ramp-up period`

斜坡上升周期，递增时间，单位：seconds

即在多少秒内递增到设置的线程数

除了秒杀，在大多数场景中，递增都是必不可少的过程，而且要连续

```text
Number of Threads 100
Ramp-up period 10

1s = 1000ms
100/(10*1000) = 1线程/100ms
即每 100ms 启动 1 个线程
```

需要合理的设置 Ramp-up 时间，不能太长也不能太短，比如 5 秒启动 1000 个线程，平均每秒 200 个，但受限于硬件资源，这 200 个线程可能并不会都在 1s 内启动，而且启动过多的线程还可能会导致 JVM 内存溢出

递增的经验值

![20230830001640](https://image.zuoright.com/20230830001640.png)

在每个阶梯递增的过程中，出现了抖动，这就明显是系统设置的不合理导致的，有两种可能性

- 数据没有预热
- 资源的动态分配不合理，像后端线程池、内存、缓存等等

### 其它参数

- `Loop Count`

循环次数

如果希望一直压下去，可以设置为 `Infinite`（无限），然后勾选调度器 `Specify Thread lifetime` 指定线程生命周期

- Duration

持续时间，单位：seconds

如果同时设置了 Loop Count 和 Duration，则压测时间为：$min(Loop Count * 响应时间, Duration)$，通常不建议这样做

- Startup Delay

线程组启动延迟，单位：seconds

当测试开始时，JMeter 将等待 Startup Delay 秒，然后在 Ramp-up period 内启动 Number of Threads 个线程，并运行 Duration 秒

- `Delay Thread creation until needed`

不勾选，一开始便创建所有线程，勾选后，则需要时才会创建

通常不勾选，线程数较多时需要勾选，否则一开始创建线程会很消耗 CPU

## 请求设置

### 设置请求的默认参数

HTTP Request Defaults

![20231030191437](https://image.zuoright.com/20231030191437.png)

### 设置请求头

HTTP Header Manager

`Content-Type` 默认为 `application/x-www-form-urlencoded`，如果请求是 `Content-Type:application/json` 需要设置

### 设置请求参数格式

![20231030194857](https://image.zuoright.com/20231030194857.png)

如果是 `x-www-form-urlencoded` 格式，直接使用 Parameters 即可，请求头 `Content-Type` 默认即可

如果是 `form-data` 格式，需要勾选 `Use multipart/form-data`，然后使用 Parameters，并且请求头的 `Content-Type` 需要设置为 `Content-Type:application/json`

如果是 `json` 格式，则使用 Body Data，请求头设置为 `Content-Type:application/json`

## 同步定时器

Synchronizing Timer

- `Number of Simultaneous Users to Group by` 设置为需要集合的线程数
- `timeout in milliseconds` 为超时时间，即多少秒没集合到指定人数就超时

## 参数化

以 CSV Data Set Config 组件为例

![20230823201405](https://image.zuoright.com/20230823201405.png)

- `Filename` 数据文件路径
- `Variable Names` 指定用户名和密码对应的变量名，英文逗号分隔，对应 CSV 文件中的两列数据，通过用户名在其他组件中引用
- `Allow quoted data?` 选项设置为 `True` 表示数据中的引号 `"` 不会被编码为 `%22`
- `Recycle on EOF?` 循环一遍后（遇到文件结束符）是否再循环，`Edit` 表示根据自定义内容调用函数或变量
- `Stop thread on EOF?` 循环一遍后是否停止线程，不停止且不循环的话可能会导致参数不足
- `Sharing mode` 参数生效的范围，`Edit` 可指定线程组，比如只在1、3、5线程组中生效，可输入：`SharedWithThreadGroup1and3and5`

## 获取 SQL 数据

- 下载驱动

<https://dev.mysql.com/downloads/connector/j/>

解压后将 `mysql-connector-j-8.1.0.jar` 放入 `apache-jmeter-5.6.2/lib` 路径下

![20230821152014](https://image.zuoright.com/20230821152014.png)

重启 Jmeter 后创建 JDBC 连接器来配置

> URL 格式：`jdbc:mysql://host:port/db`

![20230821152410](https://image.zuoright.com/20230821152410.png)

## 获取 Redis 数据

### 方法1：插件 Redis Data Set

> 仅支持 List 和 Set 类型，且只读

插件管理器中搜索 redis 插件安装并重启

![20230821171606](https://image.zuoright.com/20230821171606.png)

添加配置元件 `jp@gc - Redis Data Set` 来设置

![20230821191134](https://image.zuoright.com/20230821191134.png)

### 方法2：Jedis

[下载 Jedis 的 Jar 包](https://mvnrepository.com/artifact/redis.clients/jedis/4.4.3)，放入 `apache-jmeter-5.6.2/lib/ext` 路径下

![20230821214120](https://image.zuoright.com/20230821214120.png)

添加 BeanShell Sampler，根据业务编写脚本

## BeanShell Sampler

BeanShell 提供了一种在 Java 中编写和执行脚本的方式，使开发人员能够以脚本的形式编写和执行 Java 代码，而无需事先编译成字节码

- <https://jmeter.apache.org/usermanual/component_reference.html#BeanShell_Sampler>
- <https://github.com/beanshell/beanshell>

### 获取 Redis 值

```java
import redis.clients.jedis.Jedis;
import java.util.Map;

String host = "xxx.xx.xxx.xx";
int port = xxxx;
String password = "xxxx";

Jedis jedis = new Jedis(host, port);
jedis.auth(password);

int index = 11;
jedis.select(index);

String key = "VERIFICATION_CODE:EMAIL:xxxx";
String field = "code";

String fieldValue = jedis.hget(key, field);

vars.put("value", fieldValue);  // 可以用 ${value} 来引用获取到的验证码
```

### MD5 加密

```java
import org.apache.commons.codec.digest.DigestUtils;

String str = "xxx";
String sign = DigestUtils.md5Hex(str);

vars.put(“pwd”, sign.toUpperCase());  // 改为大写后输出
```

## JSR223 Sampler

<https://jmeter.apache.org/usermanual/component_reference.html#BeanShell_Sampler>

支持多种脚本语言，比如：BeanShell、Groovy、JS 等

也支持 Jython，但 Jython 目前不支持 Python3 语法

另外 JS 是基于 Nashorn 引擎的，目前不支持 ES6 语法

## 录制脚本

![20230814231121](https://image.zuoright.com/20230814231121.png)

![20230814232717](https://image.zuoright.com/20230814232717.png)
