---
title: "Hello.Flink.Structure"
date: "2019-05-28"
categories:
 - "整理"
tags:
 - "Flink"
toc: true
---



# 架构

- 整体架构
    - ![架构图](http://img3.tbcdn.cn/5476e8b07b923/TB1ObBnJFXXXXXtXVXXXXXXXXXX)

- 组件串联
    - `Client` 将任务提交至 `JobManager`，尤其分发至各 `TaskManager` 执行

- 组件说明

| 模块        | 子模块 | 说明                                                                           |
|-------------|--------|------------------------------------------------------------------------------|
| Client      |        | 进行任务的提交/终止<br>提交 Jar 包等资源                                       |
| JobManager  |        | 优化执行任务，Job 调度<br> Checkpoint 协调<br>汇总 TaskManager 的心跳和统计信息 |
| TaskManager |        | Slot 容器，运行指派任务（实现代码的序列化）<br>使用 Netty 进行上下节点通信                          |
|             | Slot   | Slot= JVM= Thread<br>共享 JVM/ TCP 连接/ 心跳                                  |

# 图谱

- 分类

| 图谱           | 负责节点    | 描述                                           |
|----------------|-------------|----------------------------------------------|
| StreamGraph    | Client      | Stream API 编码图                              |
| JobGraph       | Client      | 主要进行节点合并，减少数据流转（减少序列化/传输） |
| ExecutionGraph | JobManager  | JobGraph 的并行版本                            |
| RunGraph       | TaskManager | 实际运行的各 TaskManager 调度图                |

- 图示
    - ![图表展现](http://img3.tbcdn.cn/5476e8b07b923/TB1tA_GJFXXXXapXFXXXXXXXXXX)

- 名词解析

| 类型           | 属性                        | 说明                                                                                     |
|----------------|-----------------------------|------------------------------------------------------------------------------------------|
| StreamGraph    |                             |                                                                                          |
|                | StreamNode                  | Operator 类，指定有并发度/数据流入和流出                                                  |
|                | StreamEdge                  | StreamNode 的数据通道                                                                    |
| JobGraph       |                             |                                                                                          |
|                | JobVertex                   | 符合条件而合并在一起的一或多个 Operator                                                  |
|                | IntermediateDataSet         | JobVertex 的结果输出                                                                     |
|                | JobEdge                     | 数据传输通道，由 IntermediateDataSet 输入，并输出至 JobVertex                              |
| ExecutionGraph |                             |                                                                                          |
|                | ExecutionJobVertex          | 与 JobGraph.JobVertex 对应<br>ExecutionJobVertex 的集合                                  |
|                | ExecutionVertex             | ExecutionJobVertex 的并发子任务，输入为 ExecutionEdge，输出为  IntermediateResultPartition |
|                | IntermediateResult          | 与 JobGraph.IntermediateDataSet 对应<br> IntermediateResultPartition 的集合              |
|                | IntermediateResultPartition | IntermediateResult 的并发子任务，输入为 ExecutionVertex，输出至 ExecutionEdge              |
|                | ExecutionEdge               | 与 JobGraph.JobEdge 对应<br>输入为 IntermediateResultPartition，输出至 ExecutionVertex    |
|                | Execution                   |                                                                                          |
| RunGraph       |                             |                                                                                          |
|                | Task                        | 于 TaskManager.Slot 中执行相应逻辑功能的 Operator                                        |
|                | ResultPartition             | 与 ExecutionGraph.IntermediateResult 对应                                                |
|                | ResultSubpartition          | 与 ExecutionGraph.IntermediateResultPartition 对应                                       |
|                | InputGate                   | 与 ExecutionGraph.ExecutionEdge 对应<br> InputChannel 的集合                             |
|                | InputChannel                | 由 InputGate 进行实际的数据传输                                                          |

# 扩展

## 槽位
- JobGraph
    - `[Source+ Map](6)-> [keyby+ window+ apply](6)-> Sink(1)`

- RunGraph
    - ![ECv5y2.jpg](http://doc.yqjdcyy.com/99bd3da7-e4a9-4f38-86fb-3dc267459aa6.jpg)


# 参考
- [Flink 原理与实现：架构和拓扑概览](http://wuchong.me/blog/2016/05/03/flink-internals-overview/)
- [Flink 原理与实现：如何生成 JobGraph](http://wuchong.me/blog/2016/05/10/flink-internals-how-to-build-jobgraph/)
- []()
- [《从0到1学习Flink》—— Flink parallelism 和 Slot 介绍](http://www.54tianzhisheng.cn/2019/01/14/Flink-parallelism-slot)
- []()
- []()
