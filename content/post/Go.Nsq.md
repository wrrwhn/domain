+++
date = "2017-07-25T22:07:46+08:00"
title = "Go.Nsq"
draft = false
tags = ["整理","Go"]
share = true
+++

[TOC]

# [NSQ]
## 参考
- [An Example of Using NSQ From Go](http://tleyden.github.io/blog/2014/11/12/an-example-of-using-nsq-from-go/)
- [NSQ 官网](http://nsq.io/)
- [NSQ Github](https://github.com/nsqio/nsq)
- [NSQ 指南](http://wiki.jikexueyuan.com/project/nsq-guide/intro.html)

## 介绍
### NSQ
- 实时的分页式消息处理平台
- 支持无 SPOF 的分布式拓扑
- 默认消息不持久化
    - 可通过设置 `--mem-queue-size` 参数为 0 变更
- 接收到的消息无序

### 组成
- nsqlookupd
    - 作用
        - 管理拓扑结构信息的守护进程
        - 客户端查询以发现 `nsqd` 生产者的指定 `topic`
        - `nsqd`节点广播 `topic` 和 `channel` 信息
        - 接口提供
            - TCP：供 `nsqd`广播操作
            - HTTP：供客户端发现、管理性动作
    - 参数
        - `-broadcast-address`
            - `lookupd` 节点地址
            - 默认为 PROSNAKES.local
        - `-config`
            - config 文件路径
        - `-tcp-address`
            - TCP 客户端请求地址、端口
            - 默认为 0.0.0.0:`4160`
        - `-http-address`
            - HTTP 客户端请求地址、端口
            - 默认为 0.0.0.0:`4161`
- nsqd
    - 作用
        - 接口、队列存储和发送客户端消息的守护进程
        - 接口提供
            - TCP
            - HTTP
                - `stats`


    - 参数
        - `-config`
            - config 文件路径
        - `-broadcast-address`
            - `lookupd` 节点地址
            - 默认为 PROSNAKES.local
        - `-data-path`
            - 存储磁盘备份信息的地址
        - `-http-address`
            - HTTP 客户端请求地址、端口
            - 默认为 0.0.0.0:`4151`
        - `-http-client-connect-timeout`
            - HTTP 连接超时时间
            - 默认为 2s
        - `-http-client-request-timeout`
            - HTTP 请求超时时间
            - 默认为 5s
        - `-https-address`
            - HTTPS 客户端请求地址、端口
            - 默认为 0.0.0.0:`4152`
        - `-max-body-size`
            - 单指令体大小限制
            - 默认为 `5242880字节`
        - `-max-bytes-per-file`
            - 回滚前每个硬盘文件字段大小
            - 默认为 `104857600字节`
        - `-max-heartbeat-interval`
            - 客户端心跳配置最大间隔
            - 默认为 `1m0s`
        - `max-msg-size`
            - 单消息最大字节
            - 默认为 `1048576`
        - `-max-msg-timeout`
            - 消息超时的最大间隔
            - 默认为 `15m0s`
        - `-max-req-timeout`
            - 消息最大重新入队超时时间
            - 默认为 `1h0m0s`
        - `-statsd-address`
            - 推送统计信息的守护进程 UDP 地址
        - `-tcp-address`
            - TCP 客户端临时地址、端口
            - 默认为 `0.0.0.0:4150`
- nsqadmin
    - 作用
        - Web UI
        - 实时查看聚合群组状态
        - 执行多种管理任务

## 设计
- `nsqlookupd` 提供目录服务，实现 `nsqd` 生产者和实际消费者的解耦
- `nsqlookupd` 通过 TCP 连接获取 `nsqd` 的状态、地址，并通过 HTTP 连接用于轮询
- `nsqadmin` 用于聚合查询、监测和管理集群
- 配置管理
    - 单 `nsqd` 可同时处理多个数据流
    - 每个通道接收话题中的所有信息拷贝
    - 话题和通道的所有缓冲数据相互独立
    - 一个通道有多个客户端连接，则消息会传送到随机一个客户端
    - ![nsqd](http://wiki.jikexueyuan.com/project/nsq-guide/images/design1.gif)
- 消除SPOF（单点故障）
    - ![nsqds](http://wiki.jikexueyuan.com/project/nsq-guide/images/design3.png)
- 消息传递担保
    - 客户端准备好
    - NSQ 发送消息，并将数据暂存在本地(re-queue/ timeout)
    - 客户端回复 FIN 或 REQ 表示成功或失败重排。
        - 若未回复，则将于设定时间超时后，自动重排回队列
- 限定内存占用 | 持久化
    - `--mem-queue-size`配置队列在内存中的消息数量
        - 超过阈值时，消息将透明地写入磁盘
        - 消息可能会被传递两次

## 使用
- `nsqlookupd`
- `nsqd --lookupd-tcp-address=127.0.0.1:4160`
- `nsqadmin --lookupd-http-address=127.0.0.1:4161`
- `nsq_to_file --topic=test --output-dir=/tmp --lookupd-http-address=127.0.0.1:4161`
- `curl -d 'hello world 1' 'http://127.0.0.1:4151/put?topic=test'`

## 注意
- 内存使用率
    - 2.4/ 2.4/ 9.7M
- 性能
    - 默认配置启动，3节点运行 nsqd
        - 197mb/s 带宽


## 打包
### 准备
- golang 1.6+
- gpm

### 运行
- gpm install
- go build
