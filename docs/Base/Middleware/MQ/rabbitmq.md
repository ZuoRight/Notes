
# RabbitMQ

[官方文档](https://www.rabbitmq.com/getstarted.html)

```shell
# 安装
brew install rabbitmq  # Mac

# brew 启动/停止服务
brew services start rabbitmq
brew services stop rabbitmq

# 或者
rabbitmq-server  # 前台运行
rabbitmq-server -detached  # 后台运行
rabbitmqctl stop
```

> 如果更新后遇到服务启动失败的问题可以卸载重装参考这篇：<https://medium.com/@anjantalatatam/how-to-clean-install-rabbitmq-1ae214436b7d>

## `rabbitmqctl`

> <https://www.rabbitmq.com/cli.html>

安装后默认有一个 guset 用户，根据需求可以添加一些新用户并设置不同权限

```shell
rabbitmqctl --help

# 创建一个 RabbitMQ 用户、一个虚拟主机并允许该用户访问该虚拟主机
rabbitmqctl add_user user_name user_passwd  # 创建新用户
rabbitmqctl add_vhost myvhost  # 如果不设置，默认为 /
rabbitmqctl set_user_tags user_name user_tag  # 设置标签
rabbitmqctl set_permissions -p myvhost user_name '.*' '.*' '.*'  # 设置权限

# 创建一个具有完全访问权限的用户
rabbitmqctl add_user full_access s3crEt
# tag the user with "administrator" for full management UI and HTTP API access
rabbitmqctl set_user_tags full_access administrator
```

## UI管理插件 `rabbitmq_management`

> <https://www.rabbitmq.com/management.html>

```shell
rabbitmq-plugins list  # 列出插件
rabbitmq-plugins enable rabbitmq_management  # 启用管理插件
```

默认提供了基于浏览器的 UI 界面（需要授权才能访问），`http://{node-hostname}:15672/`，本机：<http://localhost:15672/>，默认用户名和密码都是：`guest`

![20240416002802](https://image.zuoright.com/20240416002802.png)

## 命令行管理工具 `rabbitmqadmin`

> <https://www.rabbitmq.com/management-cli.html>

除了执行一些与 UI 相同的操作外，还方便做一些自动化任务，需要单独下载使用

- 下载方式1：[复制对应版本的源码](https://github.com/rabbitmq/rabbitmq-server/blob/master/deps/rabbitmq_management/bin/rabbitmqadmin)  
- 下载方式2：从启用了management的节点直接下载，访问：`http://{node-hostname}:15672/cli/rabbitmqadmin`

下载后放入 `/usr/local/bin`，源码是 Python3 写的，所以运行需要依赖 Python3

```shell
rabbitmqadmin --help
rabbitmqadmin -V test list exchanges
```

## 客户端

不同编程语言都有封装好的 [Clients Libraries and Developer Tools](https://www.rabbitmq.com/devtools.html)，比如 Python 有

### Pika

> [官方文档](https://pika.readthedocs.io/en/stable/)  
> [RabbitMQ 官方示例](https://www.rabbitmq.com/getstarted.html)

Pika比较小巧，仅支持 AMQP 0.9.1 协议

`pip install pika`

### Kombu

> [官方文档](https://docs.celeryq.dev/projects/kombu/en/latest/index.html)

Kombu 是为 Celery 而生的，它实现了对 AMQP transport（RabbitMQ） 和 non-AMQP transports（Redis）的兼容，相比于 Pika，支持更多的协议，功能也更丰富，代码实现也更抽象。

`pip install kombu`

## 消息模型

> <https://www.rabbitmq.com/getstarted.html>

### Product/Consumer

一对一生产消费模型

![20240415220259](https://image.zuoright.com/20240415220259.png)

在发送消息前需要确保接收队列存在，否则消息将被丢弃

```shell
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

依次向每个消费者发送消息，消费者之间属于竞争关系

![20240415220517](https://image.zuoright.com/20240415220517.png)

消息确认机制：c收到message后需要给p回复一个ack，p收到ack后则会将message从队列中删除，默认需要手动回复ack，可以设置为`ack=True`，如果因为连接中断等原因30min内（默认）没有ack，message会被重新放入队列（如果忘记设置回复ack，属于是一个低级错误，message被循环重新放入队列消耗内存）

RabbitMQ默认不会持久化message，服务重启后消息将丢失，如果想持久化需要在定义队列时设置`durable=True`，并且在发送消息时添加属性`delivery_mode = pika.spec.PERSISTENT_DELIVERY_MODE`将消息标记为持久化，不过这个持久化会先保存在缓存中然后才会保存到磁盘。

```python
channel.queue_declare(queue="hello_durable", durable=True)

channel.basic_publish(
    exchange='',
    routing_key='hello_durable',
    body=message,
    properties=pika.BasicProperties(
        delivery_mode=pika.spec.PERSISTENT_DELIVERY_MODE
        """
        delivery_mode: 将消息标记为持久化
        content_type: 用于描述编码的MIME类型，比如JSON通常为application/json
        reply_to: 用于命名callback queue.
        correlation_id: 用于关联RPC的responses和requests.
        """
    )
)
```

> 注意，不能对已存在的队列修改属性，最简单的解决方式是新建一个queue

避免繁重的消息都循环给同一个worker，通过`channel.basic_qos(prefetch_count=1)`来设置worker返回ack之前，p不要再给其发送新消息，而是发给其它不忙的c

### Publish/Subscribe

发布订阅模型，一次向多个订阅者发送消息，每个订阅者都可以收到相同的消息

![20240415220624](https://image.zuoright.com/20240415220624.png)

rabbitmq中message不能直接发送给queue，需要经过exchange，exchange有`direct`、`topic`、`headers`、`fanout`几种

```shell
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
    routing_key="",  # fanout模式routing_key可以为空，p将会把消息发给所有绑定了exchange的queue
    body=message
)
```

生产者只需要将message发给exchange，不需要关心queue本身，可以创建一个临时queue

```python
# 自定义一个临时queue
channel.queue_declare(
    queue="",
    exclusive=True  # 连接关闭时，自动删除queue
)
"""
queue="" 则由系统随机命名：amq.gen-JzTY20BRgKO-HjmUJj0wLg
通过result.method.queue 获取随机生成的queue名
"""
```

消费者需要将queue与指定exchange进行绑定来接收消息，exchange会把message发给所有与其绑定的queue

```python
channel.queue_bind(exchange='logs', queue=result.method.queue)
```

```shell
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

```shell
python emit_log_topic.py "kern.critical" "A critical kernel error"
```

- c

```shell
# 接收所有消息
python c.py "#"

# 匹配
python c.py "kern.*"
python c.py "*.critical"

# 多种匹配
python c.py "kern.*" "*.critical"
```
