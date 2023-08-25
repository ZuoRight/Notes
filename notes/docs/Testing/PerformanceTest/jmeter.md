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
    如果启动后有闪退或者报错等问题，大概率跟jdk版本有关，我在 `jdk-11.0.11` 及以上版本都有遇到过各种问题，然后降低到 `jdk1.8.0_311.jdk` 版本后就稳定多了，哎。

## 插件管理器

下载插件Jar包：<https://jmeter-plugins.org/wiki/PluginsManager/>

将 `jmeter-plugins-manager-1.10.jar` 放入 `apache-jmeter-5.6.2/lib/ext` 路径下

然后在菜单栏 `Options` 选项中即可看到 `Plugins Manager` 入口

![20230821172152](https://image.zuoright.com/20230821172152.png)

## 测试组件

> <https://jmeter.apache.org/usermanual/component_reference.html>

[Test Plan](https://jmeter.apache.org/usermanual/test_plan.html) 描述了 Jmeter 在运行时将要执行的一系列步骤

- Config Element 各种配置元素，比如HTTP请求默认配置、计数器、JDBC连接配置、CSV配置等
- Samplers 采样器，添加各种请求
- Logic Controllers 逻辑控制器，控制如何发送请求
- Timers 定时器，比如同步定时器可以阻塞线程，达到一定数量后同时下发
- Pre Processor 预处理器，发出采样请求前执行的操作
- Post Processor 后处理器，发出采样请求后执行的操作，通常用于处理响应数据，比如正则或者解析JSON获取下一个请求的依赖值等
- Assertions 断言
- Listeners 侦听器，查看请求响应和断言结果等

### Threads

线程组是任何测试计划的起点，所有 Samplers 和 Controllers 必须位于线程组下，其它组件若放在测试计划下则表示应用于所有线程组

- `Number of Threads (users)` 线程数，用户数，产生TPS
- `Ramp-up period (seconds)` 斜坡上升周期，递增时间
- `Loop Count` 循环次数

需要合理的设置 Ramp-up 时间，不能太长也不能太短，比如5秒启动1000个进程，平均每秒200个，但受限于硬件资源，这200个进程可能并不会都在1s内启动，启动过多的线程还可能会导致JVM内存溢出

如果希望一直压下去，可以将 Loop Count 设置为 `Infinite`（无限），然后勾选调度器 `Specify Thread lifetime` 指定线程生命周期

- Duration (seconds) 持续时间
- Startup Delay (seconds) 线程组启动延迟

> 如果同时设置了 Loop Count 和 Duration，则压测时间为：`min(Loop Count * 响应时间, Duration)`，通常不建议这样做

当测试开始时，JMeter 将等待 Startup Delay 秒，然后在 Ramp-up period 内启动 Number of Threads 个线程，并运行 Duration 秒

`Delay Thread creation until needed` 通常不勾选，让其一开始创建所有线程，若勾选后，则需要时才会创建，通常线程数较多时需要勾选，否则一开始创建线程会很消耗 CPU

## 参数化

以 `CSV Data Set Config` 为例

![20230823201405](https://image.zuoright.com/20230823201405.png)

CSV 文件中有两列，分别指定变量名，可在其他组件中引用

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
