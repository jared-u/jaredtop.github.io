---
title: kafka相关操作及报错
author: Jared
tags:
  - kafka
  - zookeeper
categories:
  - [kafka]
  - [zookeeper]
abbrlink: 18e29e3e
date: 2020-08-26 01:21:01
---
### 启动zookeeper和kafka
<!-- more -->
```shell
启动zookeeper
nohup ./kafka_2.13-2.6.0/bin/zookeeper-server-start.sh ./kafka_2.13-2.6.0/config/zookeeper.properties &
启动kafka
nohup ./bin/kafka-server-start.sh config/server.properties &

创建topic
./bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic hello
./bin/kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092
查看topic
./bin/kafka-topics.sh --list --zookeeper localhost:2181
./bin/kafka-topics.sh --describe --topic quickstart-events --bootstrap-server localhost:9092
生产消息
./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic hello
./bin/kafka-console-producer.sh --topic quickstart-events --bootstrap-server localhost:9092

```

### 报错解决方法

```
创建消费者时候报错：

./bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic hello --from-beginning
zookeeper is not a recognized option
问题原因：
新版本kafka中，--zookeeper这种启动方式已删除。
解决办法：
0.90版本之后启动消费者的方法如下：

./bin/kafka-console-consumer.sh --bootstrap-server localhost:2181 --topic hello --from-beginning

```

