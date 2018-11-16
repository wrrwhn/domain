---
title: "Apache.Beam的前世今生"
date: "2017-11-16"
categories:
 - "整理"
tags:
 - "Beam"
toc: true
---

## [Apache Beam的前世今生](https://mp.weixin.qq.com/s?__biz=MjM5MDE0Mjc4MA==&mid=2650995344&idx=1&sn=a3cfe5a7b300364aa63ade1672d360c2)

### 开源原因
- 意识到开源软件的巨大价值
- 足够多模型才能促使项目成功

### 简介
- 基础
    + Cloud Dataflow
        * 介绍
            - 构建、管理和优化复杂数据流水线的方法
            - 用于构建移动应用、调试、追踪和监控产品级云应用
        * 技术
            - Flume：数据高效并行处理
            - MilWhell：互联网级别流处理，优秀的容错机制
        * ![6998b780-e9df-11e6-b96d-0071cc916200.png](http://img.yqjdcyy.com/6998b780-e9df-11e6-b96d-0071cc916200.png)
- 介绍
    + 统一编程框架，提供开源、统一的编程模型
- 特点
    + 统一：对于批处理和流式处理，使用单一的编程模型
    + 可移植：可以支持多种执行环境，包括Apache Apex、Apache Flink、Apache Spark和谷歌Cloud Dataflow等
    + 可扩展：可以实现和分享更多的新SDK、IO连接器、转换操作库等
- 适用场景
    + 并发数据处理任务
        * 将要处理的数据集分解成许多相互独立而又可以并行处理的小集合
    + ETL任务
    + 数据整合
- **组成**
    + Beam SDK
        * 针对各数据来源、形式，采用相同的数据模型处理、封装
    + Beam Pipeline Runner
        * 将 Beam 模型定义开发的处理流程，翻译成底层分布式数据处理平台支持的运行时环境
        * 需要指明底层的 Runner
            - 目前支持 Flink/ Spark/ Apex/ Cloud Dataflow

### 设计
- 问题
    + 数据
        * 已持久化的有限数据集，如 HDFS文件，HBase 表
        * 实时数据流，如 Kafka中的系统日志流，Twitter 流
        * 注：有限数据集可看为实时数据流的一个子集
    + 时间
        * process time：数据进入分布式处理框架时间
        * event time：数据产生时间
    + 乱序
        * 数据到达时间与 event-time 无法完全符合
- 解决方法(WWWH)
    + [W]hat are you computing
        * Element-Wise    元素级
        * Aggregating     聚合
        * Composite        复合
    + [W]here in event time
        * ![bbd89166-e9ea-11e6-aed9-0071cc916200.png](http://img.yqjdcyy.com/bbd89166-e9ea-11e6-aed9-0071cc916200.png)
    + [W]hen in processing time
        * 使用 Trigger 机制，配合 SDK 中的水位线和触发器进行时机指定
    + [H]ow do refinements relate
        * 指定如何处理迟到数据，增量或合并输出
        * 由 SDK 中的 Accumulation 指定
