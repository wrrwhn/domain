+++
date = "2017-11-15T22:07:46+08:00"
title = "Kafka"
draft = false
tags = ["整理","Kafka"]
share = true
+++

# Kafka

## 参考
- [Kafka剖析（一）：Kafka背景及架构介绍](http://www.infoq.com/cn/articles/kafka-analysis-part-1)
- [Apache kafka 工作原理介绍](https://www.ibm.com/developerworks/cn/opensource/os-cn-kafka/index.html)
- [Quickstart](https://kafka.apache.org/quickstart)


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
    - ![0310020.png](http://otzm88f21.bkt.clouddn.com/8cae96c5-0537-43ec-a7f3-ce16df61e6b8.png)
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
                            - ![0310022.png](http://otzm88f21.bkt.clouddn.com/6080ca25-8e46-47e2-9970-f4099f6202a5.png)
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
- 暂略
