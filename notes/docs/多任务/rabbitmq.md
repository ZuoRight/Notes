# RabbitMQ

[官方文档](https://www.rabbitmq.com/getstarted.html)（[中文](http://rabbitmq.mr-ping.com/)）

```bash
# 更新brew
brew update
# 安装
brew install rabbitmq
# 启动服务
brew services start rabbitmq
```

不同编程语言都有封装好的[Clients Libraries and Developer Tools](https://www.rabbitmq.com/devtools.html)，比如Python有：pika、celery/kombu

## pika

[rabbitmq官方示例-基于pika](http://rabbitmq.mr-ping.com/tutorials_with_python/[1]Hello_World.html)

[pika官方文档](https://pika.readthedocs.io/en/stable/)

## kombu

[kombu官方文档](https://docs.celeryq.dev/projects/kombu/en/latest/index.html)

Celery是Python中最流行的异步消息队列框架，支持RabbitMQ、Redis、ZoopKeeper等作为Broker，而对这些消息队列的抽象，都是通过Kombu实现的。Kombu实现了对AMQP transport和non-AMQP transports(Redis、Amazon SQS、ZoopKeeper等)的兼容。

`pip install kombu`
