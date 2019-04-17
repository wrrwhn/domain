

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
    实际的生命线，正常淹没过的 window 都触发/清空
lateness
    表示能在水位线淹过多久时间内，仍然存活
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
| 15        | 15        | 10/ 15    | stash   |

context.timestamp(), context.currentProcessingTime(), context.currentWatermark()
                                
Sink[1553170264999, 1553170262547, 1553170261000]: 
	(1,1553170261000)
Sink[1553170269999, 1553170262548, 1553170266000]: 
	(5,1553170265000)
	(6,1553170266000)
Sink[1553170274999, 1553170262548, 1553170270000]: 
	(10,1553170270000)
Sink[1553170279999, 1553170262548, 1553170275000]: 
	(15,1553170275000)


## eventtime+ window(time.second(5))+ maxOutOfOrderness(time.second(2))

| EventTime | Watermark | Print   | Operate |
|-----------|-----------|---------|---------|
| 1         | -1        |         | [0,5)        |
| 5         | 3         | 1       |  [0,5)       |
| 6         | 4         |         | [5,10)        |
| 4         | 4         |         | [0,5)        |
| 2         | 4         |         | [0,5)        |
| 10        | 8         | 1,5,4,2 | [10,15)        |
| 3         | 8        |         | X        |
| 15        | 13        | 10      |  [15,20)       |
| 15        | 13        | 10      |  [15,20)       |

Sink[1553170264999, 1553170316880, 1553170264000]: 
	(1,1553170261000)
	(4,1553170264000)
	(2,1553170262000)
Sink[1553170269999, 1553170316881, 1553170268000]: 
	(5,1553170265000)
	(6,1553170266000)
Sink[1553170274999, 1553170316881, 1553170273000]: 
	(10,1553170270000)
Sink[1553170279999, 1553170316881, 1553170273000]: 
	(15,1553170275000)


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

Sink[1553170324999, 1553170375891, 1553170321000]: 
	(1,1553170321000)
Sink[1553170324999, 1553170375892, 1553170326000]: 
	(1,1553170321000)
	(4,1553170324000)
Sink[1553170324999, 1553170375892, 1553170326000]: 
	(1,1553170321000)
	(4,1553170324000)
	(2,1553170322000)
Sink[1553170329999, 1553170375892, 1553170326000]: 
	(5,1553170325000)
	(6,1553170326000)
Sink[1553170334999, 1553170375896, 1553170330000]: 
	(10,1553170330000)
Sink[1553170339999, 1553170375896, 1553170335000]: 
	(15,1553170335000)


## eventtime+ window(time.second(5))+ maxOutOfOrderness(time.second(1))+ lateness(time.second(2))

1L, 7L, 4L, 8L, 4L, 11L, 10L, 3L, 15L

Sink[1553234344999, 1553234351009, 1553234340000]: 
	(1,1553234341000)
Sink[1553234344999, 1553234351010, 1553234346000]: 
	(1,1553234341000)
	(4,1553234344000)
Sink[1553234349999, 1553234351010, 1553234347000]: 
	(7,1553234347000)
	(8,1553234348000)
Sink[1553234354999, 1553234351010, 1553234354000]: 
	(11,1553234351000)
	(10,1553234350000)
Sink[1553234359999, 1553234351010, 1553234354000]: 
	(15,1553234355000)




窗口触发条件
    watermark >= window.endTime
    windows.data.size> 0



AssignerWithPunctuatedWatermarks
    由特定元素提取后发出水印
AssignerWithPeriodicWatermarks
    由系统定期去生成水印
        定期时长由 getAutoWatermarkInterval 获取 



根据 keyby.window 进行水印的分开管理
	internalTimerService.registerEventTimeTimer(window, time)



# 参考
- []()
- [Flink EventTime和Watermarks案例分析](https://blog.csdn.net/xu470438000/article/details/83271123)
- []()
- []()
- []()
- []()
- []()
- []()


