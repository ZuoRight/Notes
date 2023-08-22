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

[Test Plan](https://jmeter.apache.org/usermanual/test_plan.html) 描述了 Jmeter 在运行时将要执行的一系列步骤。

完整的测试计划将由以下部分组成

- Config Element 各种配置元素，比如HTTP请求默认配置、计数器、JDBC连接配置、CSV配置等
- Threads 线程组，任何测试计划的起点
- Samplers 采样器，添加各种请求
- Logic Controllers 逻辑控制器，控制如何发送请求
- Timers 定时器，比如可以让所有进程都准备好后再一起执行
- Pre Processor 预处理器，发出采样请求前执行的操作
- Post Processor 后处理器，发出采样请求后执行的操作，通常用于处理响应数据，比如正则或者解析JSON获取下一个请求的依赖值等
- Assertions 断言
- Listeners 侦听器，查看请求响应和断言结果等

Samplers 和 Controllers 只能位于线程组下，其它组件若放在测试计划下则表示应用于所有线程组

![20230818191812](https://image.zuoright.com/20230818191812.png)

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
