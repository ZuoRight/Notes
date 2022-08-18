# 消息队列

消息队列（Message Queue），简称MQ，指保存消息的一个容器，本质上是个队列。用于上下游解耦、非即时消息异步处理、流量削峰、日志处理等场景

常见的与消息队列相关的协议或标准有

- `JMS` Java MessageService，实际上是个Java API
- `AMQP` Advanced Message Queuing Protocol，最初用于解决金融领域跨平台消息传递问题，生产者与消费者可以使用不同语言开发
- 其它：`MQTT`、`STOMP`、`OpenMessaging`

目前为止还没有谁能统一标准，基于以上标准和协议开发的消息队列有

> 参考自[《极客时间·消息队列高手课：该如何选择消息队列？》](https://time.geekbang.org/column/article/109750)

- `ActiveMQ` 最老牌的开源消息队列，是十年前唯一可供选择的开源消息队列，目前已进入老年期，社区也不再活跃。无论是功能还是性能方面，ActiveMQ 都与现代的消息队列存在明显的差距，它存在的意义仅限于兼容那些还在用的爷爷辈儿的系统。
- `RabbitMQ` 使用最广泛的轻量级MQ，开箱即用，支持多语言客户端，支持AMQP/SMTP/STOMP等多种协议，由小众语言Erlang编写，二次开发较难，性能也比其它MQ要差很多。
- `RocketMQ` 由阿里12年使用Java开发，17年捐赠给Apache软件基金会，性能比RabbitMQ高一个数量级，中文社区活跃，但在国外可能不怎么流行，兼容性较差，主要场景是处理在线业务。
- `Kafka` 由Linkedin使用Scala和Java开发，后开源给Apache软件基金会，最初用于处理海量日志，目前是生态兼容最好的MQ，尤其在大数据和流计算领域，缺点是同步收发消息的响应时延比较高，不太适合在线业务场景。
- `ZeroMQ` 严格来说只是一个基于消息队列的多线程网络库，如果你的需求是将消息队列的功能集成到你的系统进程中，可以考虑使用ZeroMQ。
- `Pulsar` 新兴的开源MQ，最初由Yahoo开发，现在也属于Apache软件基金会，采用存储和计算分离的设计，目前还处于成长期
- `Redis` 也支持MQ功能

## RabbitMQ

[官方文档](https://www.rabbitmq.com/getstarted.html)

```bash
# 安装
brew install rabbitmq
# 启动服务
rabbitmq-server  # 前台
brew services start rabbitmq  # 后台
```

### Command Line Tools

> 文档：<https://www.rabbitmq.com/cli.html>

```text
rabbitmqctl 用于服务管理和一般操作任务，通常只对管理员可用
rabbitmq-diagnostics 用于诊断和健康检查
rabbitmq-plugins 用于插件管理
rabbitmq-queues 用于队列上的维护任务，特别是仲裁队列
rabbitmq-upgrade 用于与升级相关的维护任务
```

- `rabbitmqctl`

```bash
rabbitmqctl --help
rabbitmqctl add_user user_test user_test  # 创建新用户
rabbitmqctl set_user_tags user_test administrator  # 设为管理员
rabbitmqctl set_permissions -p / user_test '.*' '.*' '.*'  # 设置权限
```

- `rabbitmq_management`

RabbitMQ的Web管理工具，文档：<https://www.rabbitmq.com/management.html>

```bash
rabbitmq-plugins list  # 列出插件
rabbitmq-plugins enable rabbitmq_management  # 启用管理插件
rabbitmq-plugins enable rabbitmq_tracing  # 开启消息追踪
```

默认提供了基于浏览器的UI界面（需要授权才能访问）：`http://{node-hostname}:15672/`

> 本机访问：<http://localhost:15672>，默认用户名和密码都是：`guest`

也可以使用management-cli，除了执行一些与UI相同的操作外，还方便做一些自动化任务，命令行工具`rabbitmqadmin`需要单独下载使用

> 文档：<https://www.rabbitmq.com/management-cli.html>
>
> 下载方式1：[复制对应版本的源码](https://github.com/rabbitmq/rabbitmq-server/blob/master/deps/rabbitmq_management/bin/rabbitmqadmin)  
> 下载方式2：从启用了management的节点直接下载，访问：`http://{node-hostname}:15672/cli/rabbitmqadmin`

下载后放入`/usr/local/bin`，源码是Python3写的，所以运行需要依赖Python3

```bash
rabbitmqadmin --help
rabbitmqadmin -V test list exchanges
```

### 客户端

不同编程语言都有封装好的[Clients Libraries and Developer Tools](https://www.rabbitmq.com/devtools.html)，比如Python有

- Pika: `pip install pika`

> [官方文档](https://pika.readthedocs.io/en/stable/)  
> [RabbitMQ 官方示例](https://www.rabbitmq.com/getstarted.html)

- Celery/Kombu: `pip install kombu`

> [官方文档](https://docs.celeryq.dev/projects/kombu/en/latest/index.html)

Celery是Python中最流行的异步消息队列框架，支持RabbitMQ、Redis、ZoopKeeper等作为Broker，而对这些消息队列的抽象，都是通过Kombu实现的。Kombu实现了对AMQP transport和non-AMQP transports(Redis、Amazon SQS、ZoopKeeper等)的兼容。

Pika比较小巧，仅支持 AMQP 0.9.1 协议，Kombu是为Celery而生的，相比于Pika，支持更多的协议，功能也更丰富，代码实现也更抽象。

## RabbitMQ 消息模型

> 文档：<https://www.rabbitmq.com/getstarted.html>

### Product/Consumer

一对一生产消费模型

在发送消息前需要确保接收队列存在，否则消息将被丢弃

```bash
# 列出queue
rabbitmqctl list_queues
"""
Timeout: 60.0 seconds ...
Listing queues for vhost / ...

name    messages
example-queue   0
hello   0
"""
```

### Work Queues

循环分发模型，每个c/worker依次收到message

消息确认机制：c收到message后需要给p回复一个ack，p收到ack后则会将message从队列中删除，默认需要手动回复ack，可以设置为`ack=True`，如果因为连接中断等原因30min内（默认）没有ack，message会被重新放入队列（如果忘记设置回复ack，属于是一个低级错误，message被循环重新放入队列消耗内存）

RabbitMQ默认不会持久化message，服务重启后消息将丢失，如果想持久化需要在定义队列时设置`durable=True`，并且在发送消息时添加属性`delivery_mode = pika.spec.PERSISTENT_DELIVERY_MODE`，不过这个持久化会先保存在缓存中然后才会保存到磁盘。

> 注意，不能对已存在的队列修改属性，最简单的解决方式是新建一个queue

避免繁重的消息都循环给同一个worker，通过`channel.basic_qos(prefetch_count=1)`来设置worker返回ack之前，p不要再给其发送新消息，而是发给其它不忙的c

### Publish/Subscribe

发布订阅模型，将一份消息发给多个订阅者，每个订阅者都可以收到相同的消息

rabbitmq中message不能直接发送给queue，需要经过exchange，exchange有`direct`、`topic`、`headers`、`fanout`几种

```bash
# 列出exchange
rabbitmqctl list_exchanges
"""
Listing exchanges for vhost / ...

name                    type
(未命名)                 direct  (默认)
example-exchange        direct
amq.direct              direct
amq.topic               topic
amq.rabbitmq.trace      topic
amq.match               headers
amq.headers             headers
amq.fanout              fanout
"""
```

#### fanout

```python
# 自定义一个exchange，生产者和消费者两边都需要声明
channel.exchange_declare(
    exchange='logs', 
    exchange_type='fanout'
)

channel.basic_publish(
    # 使用自定义的exchange
    exchange="logs",  # 如果exchange=""，则默认使用未命名exchange
    routing_key="", 
    body=message
)
```

生产者只需要将message发给exchange，不需要关心queue本身，可以创建一个临时queue

```python
# 自定义一个临时queue
channel.queue_declare(
    queue="",  # queue=""则随机命名
    exclusive=True  # 连接关闭时，自动删除queue
)
```

消费者需要将queue与指定exchange进行绑定来接收消息，exchange会把message发给所有与其绑定的queue

```python
channel.queue_bind(exchange='logs', queue=result.method.queue)
```

```bash
# 列出绑定
rabbitmqctl list_bindings
"""
Listing bindings for vhost /...

source_name         source_kind     destination_name    destination_kind        routing_key     arguments
default             exchange        example-queue       queue                   example-queue      []
example-exchange    exchange        example-queue       queue                   BOB                []
default             exchange        hello               queue                   hello              []
"""

# 可以将接收的日志转存到log文件
python receive_logs.py > logs_from_rabbit.log
```

#### direct

`exchange=direct`时，可以选择接收指定`routing_key=xxx`的消息

- p

```python
channel.basic_publish(exchange='direct_logs', routing_key=severity, body=message)
```

`python p.py error "this is an error message"`

- c

```python
for severity in severities:
    channel.queue_bind(exchange='direct_logs', queue=queue_name, routing_key=severity)
```

`python c.py warning error > logs_from_rabbit.log`

#### topic

可以通过通配符匹配`routing_key`来接收消息

- p

```bash
python emit_log_topic.py "kern.critical" "A critical kernel error"
```

- c

```bash
# 接收所有消息
python c.py "#"

# 匹配
python c.py "kern.*"
python c.py "*.critical"

# 多种匹配
python c.py "kern.*" "*.critical"
```
