# 消息队列

消息队列（Message Queue），简称MQ，指保存消息的一个容器，本质上是个队列。用于上下游解耦、非即时消息异步处理、流量削峰、日志处理等场景

常见的与消息队列相关的协议或标准有

- `JMS` Java MessageService，实际上是个Java API
- `AMQP` Advanced Message Queuing Protocol，最初用于解决金融领域跨平台消息传递问题，生产者与消费者可以使用不同语言开发
- 其它：`MQTT`、`STOMP`、`OpenMessaging`

目前为止还没有谁能统一标准，基于以上标准和协议开发的消息队列有

- `ActiveMQ` 最老牌的开源消息队列，是十年前唯一可供选择的开源消息队列，目前已进入老年期，社区也不再活跃。无论是功能还是性能方面，ActiveMQ 都与现代的消息队列存在明显的差距，它存在的意义仅限于兼容那些还在用的爷爷辈儿的系统。
- `RabbitMQ` 使用最广泛的轻量级MQ，开箱即用，支持多语言客户端，支持AMQP/SMTP/STOMP等多种协议，由小众语言Erlang编写，二次开发较难，性能也比其它MQ要差很多。
- `RocketMQ` 由阿里12年使用Java开发，17年捐赠给Apache软件基金会，性能比RabbitMQ高一个数量级，中文社区活跃，但在国外可能不怎么流行，兼容性较差，主要场景是处理在线业务。
- `Kafka` 由Linkedin使用Scala和Java开发，后开源给Apache软件基金会，最初用于处理海量日志，目前是生态兼容最好的MQ，尤其在大数据和流计算领域，缺点是同步收发消息的响应时延比较高，不太适合在线业务场景。
- `ZeroMQ` 严格来说只是一个基于消息队列的多线程网络库，如果你的需求是将消息队列的功能集成到你的系统进程中，可以考虑使用ZeroMQ。
- `Pulsar` 新兴的开源MQ，最初由Yahoo开发，现在也属于Apache软件基金会，采用存储和计算分离的设计，目前还处于成长期
- `Redis` 也支持MQ功能

## RabbitMQ

[官方文档](https://www.rabbitmq.com/getstarted.html)（[中文](http://rabbitmq.mr-ping.com/)）

```bash
# 安装
brew install rabbitmq
# 启动服务
rabbitmq-server  # 前台
brew services start rabbitmq  # 后台
```

### Command Line Tools

> <https://www.rabbitmq.com/cli.html>

- `rabbitmqctl` 用于服务管理和一般操作任务，通常只对管理员可用
- `rabbitmq-diagnostics` 用于诊断和健康检查
- `rabbitmq-plugins` 用于插件管理
- `rabbitmq-queues` 用于队列上的维护任务，特别是仲裁队列
- `rabbitmq-upgrade` 用于与升级相关的维护任务

#### `rabbitmqctl`

```bash
rabbitmqctl --help
rabbitmqctl add_user user_test user_test  # 创建新用户
rabbitmqctl set_user_tags user_test administrator  # 设为管理员
rabbitmqctl set_permissions -p / user_test '.*' '.*' '.*'  # 设置权限
```

#### `rabbitmq_management`

RabbitMQ的Web管理工具，文档：<https://www.rabbitmq.com/management.html>

```bash
rabbitmq-plugins list  # 列出插件
rabbitmq-plugins enable rabbitmq_management  # 启用管理插件
rabbitmq-plugins enable rabbitmq_tracing  # 开启消息追踪
```

默认提供了基于浏览器的UI界面（需要授权才能访问）：`http://{node-hostname}:15672/`

> 本机访问：<http://localhost:15672>，默认用户名和密码都是：`guest`

也可以使用management-cli（`rabbitmqadmin`），需要单独下载后放入`/usr/local/bin`，源码是Python3写的，所以运行需要依赖Python3

- [复制对应版本的源码](https://github.com/rabbitmq/rabbitmq-server/blob/master/deps/rabbitmq_management/bin/rabbitmqadmin)
- 从启用了management的节点直接下载，访问：`http://{node-hostname}:15672/cli/rabbitmqadmin`

除了执行一些与UI相同的操作外，还方便做一些自动化任务，文档：<https://www.rabbitmq.com/management-cli.html>

```bash
rabbitmqadmin --help
rabbitmqadmin -V test list exchanges
```

#### 客户端

不同编程语言都有封装好的[Clients Libraries and Developer Tools](https://www.rabbitmq.com/devtools.html)，比如Python有：`Pika` 和 `Celery/Kombu`

Pika比较小巧，仅支持 AMQP 0.9.1 协议，Kombu是为Celery而生的，相比于Pika，支持更多的协议，功能也更丰富，代码实现也更抽象。

### pika

[rabbitmq官方示例-基于pika](http://rabbitmq.mr-ping.com/tutorials_with_python/[1]Hello_World.html)

[pika官方文档](https://pika.readthedocs.io/en/stable/)

### kombu

[kombu官方文档](https://docs.celeryq.dev/projects/kombu/en/latest/index.html)

Celery是Python中最流行的异步消息队列框架，支持RabbitMQ、Redis、ZoopKeeper等作为Broker，而对这些消息队列的抽象，都是通过Kombu实现的。Kombu实现了对AMQP transport和non-AMQP transports(Redis、Amazon SQS、ZoopKeeper等)的兼容。

`pip install kombu`
