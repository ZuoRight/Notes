# 监控

监控是为了取证，分析是为了调优

监控要有分层、分段的能力，要有全局监控、定向监控的能力

Jmeter -> InfluxDB -> Grafana

## Jmeter 后端监听器

支持 Graphite 和 InfluxDB 两种

![20240721155347](https://image.zuoright.com/20240721155347.png)

- application 用于区分不同的 Jmeter 测试任务
- measurement 存储的数据表

## Grafana

支持从 Prometheus 和 Influxdb 等多种时间序列数据库输入数据并展示

- 添加数据源：Configuration -> Data Sources

```shell
http://influxdb:8086  # influxdb
http://prometheus:9090  # prometheus
```

- Dashboards Manage

```shell
# 面板可以自定义，也可以使用别人写好的模版
https://grafana.com/grafana/dashboards/5496  # influxdb
https://grafana.com/grafana/dashboards/8919  # prometheus
```

![20240721155242](https://image.zuoright.com/20240721155242.png)

## InfluxDB

```shell
show databases
use jmeter
show measurements
select * from jmeter limit 1;
```

## Promethues

一款开源的系统监控和报警工具，使用 Go 语言开发

有各种 xxx_exporter 来收集服务器性能数据

- node_exporter 监控主机
- mysqld_exporter 监控 MySQL
- jmx_exporter 监控 Java 服务
- haproxy_exporter 监控负载均衡
