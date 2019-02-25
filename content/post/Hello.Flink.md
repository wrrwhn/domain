
# 描述

## 适用场景
- 实时流处理
    - 热闹商品统计
    - 消息预警
- 海量数据处理
    - 传感器/网络等多种复杂来源监测
- 数据分析
    - 将复杂数据转化为用户可快速访问/理解的数据

## 支持
- 流处理
    - 实时数据流
- 批处理
    - 历史数据集
- 事件驱动应用
    - 监控事件的服务

# 技术要点

## Checkpoint
- Chandy-Lamport 算法
- 分布式一致性快照
- 通过流重放和检查点，实现失败容忍机制
    - 最新检查点之后的事件和状态更新，都将于输入流中重放

// TODO

## State
- Value|List|Map|Broadcast
- 数据流转时，有关联事件的操作被称为有状态的操作
- 状态在 KeyedStreams 中进行，并通过当前事件的 Key 进行访问
    - 本地操作，在不需要事务开销的情况下保证一致性
    
// TODO

per-window state
use keyed state 
[global|window]State    [not] scoped to a window
This feature is helpful if you anticipate multiple firing for the same window, as can happen when you have late firings for data that arrives late or when you have a custom trigger that does speculative early firings.

## Time
- Watermark 机制
    - 乱序数据处理
    - 迟到数据容忍

- 类型

| 类型           | 描述                                 |
|----------------|--------------------------------------|
| ProcessingTime | 事件被处理的时间<br>Operator= Source/ Map/ Sink|
| EventTime      | 数据本身携带的时间                   |
| IngestionTime  | 事件被 DataSource 读取的时间                                     |


https://ci.apache.org/projects/flink/flink-docs-release-1.7/fig/times_clocks.svg

App-> MQ-> Flink.DataSource-> Flink.
EventTime-> MQ-> IngestionTime-> ProcessingTime


* 重启服务，无 checkpoint 配置下，昨天的数据是怎样触发时间窗口

watermark 的生成时间

    source

Time Characteristic
    Note that in order to run this example in event time, 
        the program needs to either use sources that directly define event time for the data and emit watermarks themselves, 
        or the program must inject a Timestamp Assigner & Watermark Generator after the sources.

Event Time and Watermarks
    A Watermark(t) declares that event time has reached time t in that stream, 
        meaning that there should be no more elements from the stream with a timestamp t’ <= t 


![](https://ci.apache.org/projects/flink/flink-docs-release-1.7/fig/stream_watermark_out_of_order.svg)

Once a watermark reaches an operator, the operator can advance its internal event time clock to the value of the watermark.

![flink-window-watermark-example.webp](http://doc.yqjdcyy.com/b102ece4-582e-4ddd-b8e3-caf2e12bf0c0.webp)

Watermarks are generated at source functions
    Each parallel subtask of a source function usually generates its watermarks independently. 



// TODO

## Window
- 作用
    - 用于对一定数据的聚合计算
- 驱动
    - 时间(30s)
    - 数据量(100)
- 类型
    - global
        - 永不停止的窗口，需指定触发器
        This windowing scheme is only useful if you also specify a custom trigger
    - tumbing
        - 滚动窗口，仅需指定窗口时间跨度
        - 窗口不重叠
    - sliding
        - 滑动窗口，需指定窗口时间跨度和滑动的时长频率
        - 窗口有重叠
    - session
        - 会话窗口，开始时间和窗口大小可灵活配置
        - 窗口有空隙
         Instead a session window closes when it does not receive elements for a certain period of time,


tumbling windows, sliding windows, session windows and global windows

extending the WindowAssigner

Time-based windows have a start timestamp (inclusive) and an end timestamp (exclusive) 
without offsets hourly tumbling windows are aligned with epoch

adjust windows to timezones Time.hours(-8)
// TODO

window= 1h， 9:15开始
(9:15, 10:00], (10:00, 11:00]


Keyed Windows

stream
       .keyBy(...)               <-  keyed versus non-keyed windows
       .window(...)              <-  required: "assigner"
      [.trigger(...)]            <-  optional: "trigger" (else default trigger)
      [.evictor(...)]            <-  optional: "evictor" (else no evictor)
      [.allowedLateness(...)]    <-  optional: "lateness" (else zero)
      [.sideOutputLateData(...)] <-  optional: "output tag" (else no side output for late data)
       .reduce/aggregate/fold/apply()      <-  required: "function"
      [.getSideOutput(...)]      <-  optional: "output tag"
Non-Keyed Windows

stream
       .windowAll(...)           <-  required: "assigner"
      [.trigger(...)]            <-  optional: "trigger" (else default trigger)
      [.evictor(...)]            <-  optional: "evictor" (else no evictor)
      [.allowedLateness(...)]    <-  optional: "lateness" (else zero)
      [.sideOutputLateData(...)] <-  optional: "output tag" (else no side output for late data)
       .reduce/aggregate/fold/apply()      <-  required: "function"
      [.getSideOutput(...)]      <-  optional: "output tag"

Triggers
    determines when a window (as formed by the window assigner) is ready to be processed by the window function


    processing-time window
        ProcessingTimeTrigger 

    event-time window
        EventTimeTrigger 

    GlobalWindow 
        NeverTrigger 

    CountTrigger 
    PurgingTrigger 


Evictors
    The evictor has the ability to remove elements from a window after the trigger fires and before and/or after the window function is applied.

void evictBefore(Iterable<TimestampedValue<T>> elements, int size, W window, EvictorContext evictorContext);
    to be applied before the window function
void evictAfter(Iterable<TimestampedValue<T>> elements, int size, W window, EvictorContext evictorContext);
    to be applied after the window function


CountEvictor
    discard the number > user.limit
DeltaEvictor
    discard the delta.val> theshold
TimeEvictor
    discard the ts< max_ts- (windows.end - window.start)


 all the elements of a window have to be passed to the evictor before applying the computation.
 no guarantees about the order of the elements within a window



Lateness
event-time windowing
Elements that arrive after the watermark has passed the end of the window but before it passes the end of the window plus the allowed lateness, are still added to the window. 
In order to make this work, Flink keeps the state of windows until their allowed lateness expires
EventTimeTrigger
    a late but not dropped element may cause the window to fire again
Flink keeps the state of windows until their allowed lateness expires



sideOutputLateData
get a stream of the data that was discarded as late




# API

## 支持层次
### Analytics API
- SQL|Table API
    - dynamic tables

- 示例

    ```
    SELECT room, TUMBLE_END(rowtime, INTERVAL '1' HOUR), AVG(temp) 
    FROM sensors
    GROUP BY TUMBLE(rowtime, INTERVAL '1' HOUR), room
    ```

### Stream|Batch Data Processing
- DataStream API
    - streams, windows

- 示例

    ```
    val stats = stream
        .keyBy("sensor")
        .timeWindow(Time.seconds(5))
        .sum((a,b)-> a.add(b))
    ```

### Event-Driven Application
- ProcessFunction
    - events, state, time

- 示例

    ```
    def processElement(event: MyEvent, ctx: Context, out: Collector[Result])= {
        (event, state.value) match...}

        out.collect(..)
        state.update(..)

        ctx.timeService.registerEventTimeTimer(event.timestamp+ 500)
    }
    ```

## 方法


A windowed transformation with a ProcessWindowFunction cannot be executed as efficiently as the other cases because Flink has to buffer all elements for a window internally before invoking the function. 
.aggregate(new AverageAggregate(), new MyProcessWindowFunction());



| 转换类型                                       | 方法名             | 含义                                                                                                          |
|------------------------------------------------|--------------------|---------------------------------------------------------------------------------------------------------------|
| DataStream*-> DataStream                       |                    |                                                                                                               |
|                                                | Map                | obj{1} map(obj{1})                                                                                            |
|                                                | FlatMap            | obj* flatMap(obj{1})                                                                                          |
|                                                | Filter             | 过滤满足条件的元素                                                                                            |
|                                                | Union              | 可将多个数据流合并成一个数据流                                                                                |
|                                                | Window Join        | 合并两个数据流为一个，并通过指定键值生成/ 判断方法过滤，并添加时间窗口进行后续计算                              |
|                                                | Window CoGroup     | 数据流合并<br>同 window join 操作                                                                             |
|                                                | Extract Timestamps | 提取事件数据的时间戳，供事件时间窗口使用                                                                       |
|                                                | Iterate            | DataStream-> IterativeStream-> DataStream                                                                     |
| DataStream-> KeyedStream                       |                    |                                                                                                               |
|                                                | KeyBy              | 按 Key 拆分<br>`keyBy(0)` 表示通过第一个元素进行分组<br> 未重写 hashCode() 方法的 POJO 类和数组均无法作用 key |
| KeyedStream*->  DataStream                     |                    |                                                                                                               |
|                                                | Reduce             | 合并计算<br>仅最终结果值 <br>`val+= key`                                                                      |
|                                                | Fold               | 累计合并计算<br>过程值列表 <br>`val, val+= key, ...`                                                          |
|                                                | Aggregations       | 聚合计算<br>结合 sum/ avg/ min/ minBy/ max/ maxBy 使用                                                        |
|                                                | Max                | 返回指定字段的最小**数值**                                                                                    |
|                                                | MaxBy              | 返回包含最小数值字段的**元素**                                                                                |
|                                                | Interval Join      | 合并数据流，并指定截取的时间区间，后配合 Process 方法转换数据格式                                               |
|                                                | Process            | 底层的数据流处理函数<br>可视为具有状态和定时器访问权限的 FlatMapFunction                                      |
| KeyedStream->  WindowedStream                  |                    |                                                                                                               |
|                                                | Windows            | 结合 keyBy 对数据进行分组                                                                                     |
| DataStream->  AllWindowedStream                |                    |                                                                                                               |
|                                                | WindowAll          | 对所有数据进行分组                                                                                            |
| WindowedStream/AllWindowedStream->  DataStream |                    |                                                                                                               |
|                                                | Apply              | 支持对所有数据的操作                                                                                          |
|                                                | Reduce             | 窗口内的合并计算                                                                                              |
|                                                | Fold               | 窗口内的累计合并计算                                                                                          |
|                                                | Aggregations       | 窗口内的聚合计算                                                                                              |
| DataStream*-> ConnectedStreams                 |                    |                                                                                                               |
|                                                | Connect            | 保留数据形式地合并数据流                                                                                      |
| ConnectedStreams-> DataStream                  |                    |                                                                                                               |
|                                                | CoMap              | 连接流上的 Map 操作                                                                                           |
|                                                | CoFlatMap          | 连接流上的 FlatMap 操作                                                                                       |
| DataStream-> SplitStream                       |                    |                                                                                                               |
|                                                | Split              | 将数据打上不同标志，结合 Select 可将数据流分为多路                                                             |
| SplitStream-> DataStream                       |                    |                                                                                                               |
|                                                | Select             | 通过条件过滤出指定类型的数据流                                                                                |

# 架构

## 服务分层
- [Flink.Structure](https://ws2.sinaimg.cn/large/006tNbRwly1fw7kzdn54tj30lp0cu0td.jpg)

- 明细
    | 模块   | 细节               |
    |--------|--------------------|
    | 部署   |                    |
    |        | 本地运行           |
    |        | 独立集群           |
    |        | YARN<br> Mesos     |
    |        | Cloud              |
    | 运行   |                    |
    |        | 分布式流式数据引擎 |
    | API    |                    |
    |        | DataStream         |
    |        | DataSet            |
    |        | Table              |
    |        | SQL API            |
    | 扩展库 |                    |
    |        | 机器学习           |
    |        | 图形处理           |
    |        | 复杂事件           |    


## 抽象级别

| 抽象                  | 描述                                                                                                                           |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------|
| 有状态流              | Process.Func(Source*)<br>Event-> 复杂计算                                                                                      |
| DataStream<br>DataSet | 支持对无/有界数据的转换和计算<br>内置方法： map/ flatmap/ windows/ keyby/ sum/ max/ min/ avg/ join                              |
| Table API             | 以表（动态变化）为中心的声明式 DSL<br>API： select/ project/ join/ group-by/ aggregate<br>支持 DataStream 和 Table API 的混合使用 |
| SQL                   | 以 SQL 查询表达式形式来调用 Table API                                                                                          |


## 数据流结构 

| 名称           | 描述                                                                                                                             |
|----------------|--------------------------------------------------------------------------------------------------------------------------------|
| Source         | 数据源<br>Collection/ File/ Socket/ Custom<br>Custom: Kafka/ Klnesls/ RabbitMQ/ Twitter Streaming/ NiFi/ 自定义                  |
| Transformation | 数据转换操作<br> Map/ FlatMap/ Filter/ Windows/ Apply 等等，详见方法                                                              |
| Sink           | 接口器<br>File/ Printer/ Socket/ Custom <br> Custom: Kafka/ RabbitMQ/ MySQL/ ElasticSearch/ Cassandra/ Hadoop FileSystem/ 自定义 |


# 示例

## Watermark

### 代码

### 示例

eventtime
window(time.second(5))

| EventTime | Watermark | Print | Operate |
|-----------|-----------|-------|---------|
| 1         | 1         |       | stash   |
| 5         | 5         | 1     | stash   |
| 6         | 6         |       | stash   |
| 4         | 6         |       | discard |
| 2         | 6         |       | discard |
| 10        | 10        | 5,6   | stash   |
| 3         | 10        |       | discard |
| 15        | 15        | 10    | stash   |


eventtime
window(time.second(5))
lateness(time.second(2))

| EventTime | Watermark | Print | Operate                         |
|-----------|-----------|-------|---------------------------------|
| 1         | 1         |       | stash                           |
| 5         | 5         | 1     | stash                           |
| 6         | 6         |       | stash                           |
| 4         | 6         | 1,4   |                                 |
| 2         | 6         | 1,4,2 |                                 |
| 10        | 10        | 5,6   | stash                           |
| 3         | 10        |       | discard<br>w(10) out of [0~5+2) |
| 15        | 10        | 10    | stash                           |




# 参考

## 理解 
- [《从0到1学习Flink》—— Apache Flink 介绍](http://www.54tianzhisheng.cn/2018/10/13/flink-introduction/)
- [一文了解 Apache Flink 核心技术](http://wuchong.me/blog/2018/11/09/flink-tech-evolution-introduction/)
- [Flink学习笔记(4):基本概念](https://www.jianshu.com/p/0cd1db4282be)
- [Flink在美团的实践与应用](http://wuchong.me/blog/2018/08/25/flink-in-meituan-practice/)
- [Flink 原理与实现：Window 机制](http://wuchong.me/blog/2016/05/25/flink-internals-window-mechanism/)
- [Apache Flink 容错机制](https://segmentfault.com/a/1190000008129552)
- [Event Time / Processing Time / Ingestion Time](https://ci.apache.org/projects/flink/flink-docs-stable/dev/event_time.html)
- [深入理解Flink核心技术](https://zhuanlan.zhihu.com/p/20585530)
- []()
- [Flink 原理与实现：Session Window](http://wuchong.me/blog/2016/06/06/flink-internals-session-window/)
- []()
- []()
- []()



## 示例
- [5分钟从零构建第一个 Flink 应用](http://wuchong.me/blog/2018/11/07/5-minutes-build-first-flink-application/)
- [如何计算实时热门商品](http://wuchong.me/blog/2018/11/07/use-flink-calculate-hot-items/)
- [Flink DataStream API Programming Guide](https://ci.apache.org/projects/flink/flink-docs-stable/dev/datastream_api.html)
- [DataStream API Tutorial](https://ci.apache.org/projects/flink/flink-docs-release-1.7/tutorials/datastream_api.html)
- []()

## 官方文档
- [Operators](https://ci.apache.org/projects/flink/flink-docs-release-1.7/dev/stream/operators/)
- [Windows](https://ci.apache.org/projects/flink/flink-docs-stable/dev/stream/operators/windows.html)




# 补充
Flink= JobManager+ TaskManager*+ Client*