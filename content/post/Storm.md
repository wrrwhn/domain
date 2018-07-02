---
title: "Hello.Storm"
date: "2016-12-11"
categories:
 - "整理"
tags:
 - "框架"
 - "Storm"
toc: true
---


## 通信机制
### 选型
- 进程间使用 Netty/ ZeroMQ 进行通信
- 同一进程内的诸多线程，采用 Disruptor Queue 完成通信
- 注： LocalCluster 模式下，参数 storm.local.mode.zmq= false 则采用线程模拟进程方式启动，通过 LinkedBlockingQueue 模拟
### 进程间通信协议
- Connection
    - recv-with-flags[conn flags] - flags 表示是否为阻塞方式
    - send[conn task message]
    - close[conn]
- Context
    - bind[context storm-id port] - 绑定端口号并返回 Socket 对象，主要用于接收信息
    - connect[context storm-id host port] - 链接到指定商品号，负责发消息
    - teim[context]   - 关闭连接
- LocalCluster 模式实现
    - 采用一个进程模拟集群环境，Nimbus/ Supervisor/ Worker 均为该进程中的线程
    - Connection
        - 用 LinkedBlockingQueue 和 queue 等队列模拟消息的发送接收
    - Context
        - 用 quenes-map 和 LinkedBlockingQueue 模拟绑定和消失发送
### 分布式模式
- 实现转使用对应 ZeroMQ 或 Netty 进行绑定和相关消息发送
### 协议使用
- launch-receive-thread
    + 启动 Worker 对应的消息接收线程
- async-loop：启动一个接收线程
    - msg= taskId(recource)+ msgBody
    - 阻塞获取第一条后，切换至非阻塞方式接收 max-buffer-size 条，具体数值由 topology.receiver.buffer.size 配置确定，默认为 8
- kill-socket：
    + task == -1 时调用
- 进程内通信
    + 封装使用 Disruptor Queue 实现



## 多语言
### 使用 Thrift 的多语言支持来拓展
### ShellProcess
- 加载其它语言定义进程，并通过 STDIN STDOUT 进行通信
```
public Number launch(Map conf, TopologyContext context)
    JSONObject setupInfo= new JSONObject();     // setupInfo= workerDir/pidDir+ topology.setting+ context
public JSONObject readMessage()
    private String readString()
        case: end   消息结束
        case: Null  子进程不存在
```
### ShellBolt
- 定义读写线程，分别向子进程进行消息的发送和接收
- 使用 ShellProcess.launch 加载子进程
### ShellSpout
- querySubprocess - 核心
    - case sync: return
    - case emit: 获取消息，并根据目标 ID 有无跟踪发送
