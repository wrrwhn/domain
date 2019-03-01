

# 描述
- Flink 根据时间，将无穷的数据截取成一个个窗口，并于窗口时间到达时，将此阶段缓存的数据取出以进行下一步的操作

# 时间

## 时间类型

| 类型           | 描述                                            |
|----------------|-----------------------------------------------|
| ProcessingTime | 事件被处理的时间<br>Operator= Source/ Map/ Sink |
| EventTime      | 数据本身携带的时间                              |
| IngestionTime  | 事件被 DataSource 读取的时间                    |

## 图示

# 窗口

## 窗口类型

| 类型     | 特点                                                      |
|----------|---------------------------------------------------------|
| global   | 永不停止的窗口，需指定 Trigger                             |
| tumbling | 滚动窗口，仅需指定窗口时间跨度<br>窗口不重叠               |
| sliding  | 滑动窗口，需指定窗口时间跨度和滑动的时长频率<br>窗口可重叠 |
| session  | 会话窗口，开始时间和窗口大小可灵活配置<br>窗口有空隙       |



## 执行流程

| 方法                        | 必填 | 描述                                                                                                                                    |
|-----------------------------|------|-----------------------------------------------------------------------------------------------------------------------------------------|
| keyBy+ window<br>windowAll  | 是   | 根据键值 Hash 分组或统一处理<br>不同分组将分别维护其时间窗口                                                                                                            |
| trigger                     |      | 用于判断窗口是否准备好执行窗口操作函数                                                                                                  |
| evictor                     |      | 于触发器触发窗口操作函数执行的前后运行，用于移除窗口内的数据                                                                             |
| allowedLateness             |      | 事件时间窗口情况下使用<br>eventTime< windows.endTime+ lateness 时仍将可存入窗口<br>默认使用 EventTimeTrigger，延迟数据将再次触发窗口发送 |
| sideOutputLateData          |      |                                                                                                                                         |
| reduce/aggregate/fold/apply | 是   |                                                                                                                                         |
| getSideOutput               |      | 用于处理延迟到达，已被丢弃的数据                                                                                                         |

## 
trigger

    processing-time window
        ProcessingTimeTrigger 

    event-time window
        EventTimeTrigger 

    GlobalWindow 
        NeverTrigger 

    CountTrigger 
    PurgingTrigger 

evictor
    CountEvictor
        discard the number > user.limit
    DeltaEvictor
        discard the delta.val> theshold
    TimeEvictor
        discard the ts< max_ts- (windows.end - window.start)

watermark
lateness
idling


# 补充

## 多线程情况下数据流程


# 示例

## eventtime+ window(time.second(5))

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


## eventtime+ window(time.second(5))+ lateness(time.second(2))

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
- []()
- []()
- []()
- []()
- []()
- []()
- []()
- []()