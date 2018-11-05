---
title: "Kafka"
date: "2017-11-29"
categories:
 - "整理"
tags:
 - "Kafka"
toc: true
---


# Kafka

## 参考
- [Kafka剖析（一）：Kafka背景及架构介绍](http://www.infoq.com/cn/articles/kafka-analysis-part-1)
- [Apache kafka 工作原理介绍](https://www.ibm.com/developerworks/cn/opensource/os-cn-kafka/index.html)
- [Quickstart](https://kafka.apache.org/quickstart)
- [Kafka系列2-producer和consumer报错](http://blog.csdn.net/kuluzs/article/details/51577678)
- [Using new consumer API with a Deserializer that throws SerializationException can lead to infinite loop](https://issues.apache.org/jira/browse/KAFKA-4740)
- [How to find the kafka version in linux](https://stackoverflow.com/questions/27606065/how-to-find-the-kafka-version-in-linux)


## 简介
- Linkedin 开发，Scala编写
- 特点
    - 解耦
    - 水平扩展
    - 高吞吐率
        - 缓冲
        - 异步通信
    - 可恢复性
        - 冗余，确保安全保存至使用完毕后
        - 处理消息进程宕机，仍可于启动后重新接收处理
    - 顺序保证


## 架构
### 名词解析
- Broker
    - 集群中的服务器名称
    - 支持水平扩展的节点
- Topic
    - 集群消息中类别
- Partition
    - Topic 下包含一至多个 Partition
- Producer
    - 生产者
    - 将消息发布至 Kafka broker
- Consumer
    - 消费者
    - 向 Kafka broker 读消息的客户端
- Consumer Group
    - 各 Consumer 属于特定 Group
    - 不指定时，则属于默认 Group

### 结构拓扑
- 整体拓扑
    - ![0310020.png](http://doc.yqjdcyy.com/8cae96c5-0537-43ec-a7f3-ce16df61e6b8.png)
- 分析
    - kafka 通过 Zoopkeeper 管理集群配置
        - 选举 Leader
        - Consumer Group 变更时的 ReBalance
    - 物理上 Topic 对应一至多个 Partition
        - 每个 Partition 在物理上对应一个文件夹
            - 消息
            - 索引
        - 结构
        ```
        root    4k    topic1-1    - 日志文件
        root    4k    topic1-2
        ...
        root    4k    topic1-n
        ```
            - log entrie
                - 每个日志文件都是一个 log entrie 序列
                ```
                log entrie 内容
                    message length：    4 bytes (value: 1+4+n)
                    "magic" value：        1 byte
                    crc ：                 4 bytes
                    payload ：             n bytes
                ```
                    - segment
                        - log entries 由多个 segment 组成
                        - 以第一条消息的偏移量命名
                        - 以 `.kafka` 作为后缀
                            - `34477849968.kafka`
                                - message-34477849968
                                - message-34477850175
                                - ...
                                - message-35513434344
                            - ![0310022.png](http://doc.yqjdcyy.com/6080ca25-8e46-47e2-9970-f4099f6202a5.png)
    - 冗余
        - 保留所有消息，**无论其被消费与否**
        - 删除策略
            - 基于时间
            - 基于 Partition 文件大小
        - 配置
            - $KAFKA_HOME/config/server.properties
            ```
            # The minimum age of a log file to be eligible for deletion
            log.retention.hours=168
            # The maximum size of a log segment file. When this size is reached a new log segment will be created.
            log.segment.bytes=1073741824
            # The interval at which log segments are checked to see if they can be deleted according to the retention policies
            log.retention.check.interval.ms=300000
            # If log.cleaner.enable=true is set the cleaner will be enabled and individual logs can then be marked for log compaction.
            log.cleaner.enable=false
            ```
        - 注意
            - 为每个 Consumer Group **保留部分 metadata 信息**
                - metadata= 当前消费者的消息**偏移值**
                - broker **无状态**
                    - `不需要保证消息已经消费`
                    - `不需要保证各 Consumer Group 只有一个 Consumer 消费某一记录`


## 扩展
### Push & Pull
- Pulll
    - 由 Producer 向 broker push 消息，并由 Consumer 从 broker pull 消息
    - 根据 Consumer 的消费能力以`适当速率`消费消息
- Push
    - 目标是尽可能以最快速度传递消息
        - 容易造成拒绝服务及网络拥塞
    - Facebook.Scribe 和 Cloudera.Flume

### 消息策略
- `At most once `
    - 消息`可能丢`，但绝`不会重复`传输
        - 读完消息后，先 commit，再处理消息
- `At least one `
    - 默认项
    - 消息`绝不会丢`，但`可能会重复`传输
        - 读完消息后，先处理，完成后再 commit
- `Exactly once `
    - 每条消息肯定`会且仅传输一次`
        - 协调 offset 和实际男人出
            - 两阶段提交
            - 将 offset 与 操作输入 存放在同一地方


## 使用
- dowwnload
    - `tar -xzf kafka_2.11-1.0.0.tgz`
- server.*
    - `bin/zookeeper-server-start.sh config/zookeeper.properties`
    - `bin/kafka-server-start.sh config/server.properties`
- topic.*
    - `bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic [av]`
    - `bin/kafka-topics.sh --delete --zookeeper localhost:2181 --topic avRes`
    - `bin/kafka-topics.sh --list --zookeeper localhost:2181`
- producer
    - `bin/kafka-console-producer.sh --broker-list localhost:9092 --topic [av]`
- consumer
    - `bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic [av] --from-beginning`
- version
    - `find ./libs/ -name 'kafka_*.jar.asc' |head -n1 | cut -d'/' -f3`
    - `find ./libs/ -name \*kafka_\* | head -1 | grep -o '\kafka[^\n]*'`
        - `kafka_2.10-0.8.2-beta.jar`
            - `2.10` 
                - Scala.version
            - `0.8.2-beta`
                - Kafka.version

## 异常
### 本地生产、消费进程启动时报错
- 异常信息
    - `[2016-06-03 11:44:16,932] WARN Error while fetching metadata with correlation id 0 : {test=LEADER_NOT_AVAILABLE} (org.apache.kafka.clients.NetworkClient)`
- 修复
    - `vim config/server.properties`
        - `listeners=PLAINTEXT://localhost:9092`

### Java 接收消息时报错
- 版本
    - kafka
        - 0.10.0.0
        - 1.0.0
    - java
        - spring-kafka-1.0.3.RELEASE.jar
    - go
        - github.com/Shopify/sarama
            - Version 1.14.0
- 异常消息
    - `2017-11-28 15:46:30.927 ERROR 53444 --- [afka-consumer-1] org.apache.kafka.clients.NetworkClient   : Uncaught error in request completion: org.apache.kafka.common.errors.SerializationException: Size of data received by IntegerDeserializer is not 4`
- 解决
    - KafkaIntegerDeserializer
    ```
    public class KafkaIntegerDeserializer implements Deserializer<Integer> {

        @Override
        public Integer deserialize(String topic, byte[] data) {

            if (data.length != 4) {
                System.err.println("Size of data received by IntegerDeserializer is not 4");        // 将 throw new SerializationException 更新为提示，并跳出处理
                return null;
            }
    ```
    - KafkaConfig
    ```
    @Configuration
    public class KafkaConfig {

        private Map<String, Object> consumerConfigs() {
            props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, KafkaIntegerDeserializer.class);        // 替换 IntegerDeserializer 为 KafkaIntegerDeserializer
    ```