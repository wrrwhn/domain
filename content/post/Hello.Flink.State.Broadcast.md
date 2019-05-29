---
title: "Hello.Flink.State.Broadcast"
date: "2019-05-07"
categories:
 - "整理"
tags:
 - "Flink"
toc: true
---


# 描述
## 作用
- 将变化的数据，以广播的形式通知到关联环节或集群中的任意节点

## 场景
- 如业务处理过程中，处理规则以流的形式传入，动态变化

## 类型
### 广播元素
- 通过流连接的方式，将连接流的信息处理后，广播至被连接流中
    - 广播流接收到新数据后，便可再次广播更新
    - 广播流中的元素，被处理并广播后，**可且仅**被关联流中元素所接收
### 广播变量
- 将 `DataSet` 格式变量广播至集群中的任意节点
    - 全局仅允许发布一次，后续无法再行变更
    - 广播变量存储于各节点的内存中，不宜太大

## 细节
- `Key` 和 `Operator` 的状态，用于帮助分布式系统中各并发任务重启并恢复至最后的状态

- 广播流中对数据进行接收/处理，而接收或关联的流中，广播值**只读**

- 广播内部到各任务的**顺序不可控**
    - 避免将状态更新，与元素的接收顺序挂钩

- 各任务检查点保存时，会一并**保存广播的状态**
    - 避免恢复时，需要阅读同一文件

- 广播状态均保存在内容中
    - **没有 RocksDB** 状态的后端


# 示例
## 广播元素
- 具体事例参见 [yqjdcyy/Hello_Flink/array-broadcast-sink](https://github.com/yqjdcyy/Hello_Flink/tree/master/hello-flink/array-broadcast-sink)

```java

// 需要接收广播的流
DataSource<String> originStream= ....
// 1.描述广播流的定义
final MapStateDescriptor<String, Map<Boolean, String>> broadcastDescriptor = new MapStateDescriptor(DESCRIPTOR_BROADCAST, BasicTypeInfo.STRING_TYPE_INFO, TypeInformation.of(Map.class));
// 2.将广播来源的流转化为广播流
BroadcastStream<String> broadcastStream = env.socketTextStream(SOCKET_HOSTNAME, SOCKET_PORT).broadcast(broadcastDescriptor);
originSource
    // 3.将广播流关联至接收流
    .connect(broadcastStream)
    // 4.处理广播流和关联流中的元素
    .process(new ProcessFunction(broadcastDescriptor));
        // 4.1 根据场景选择处理方式
        extends BroadcastProcessFunction/KeyedBroadcastProcessFunction
            // 4.1.1.获取广播元素后，以此处理接收广播流的流中的数据    
            processElement(final IN1 value, final ReadOnlyContext ctx, final Collector<OUT> out){
                // 获取广播元素
                ReadOnlyBroadcastState<String, Map<Boolean, String>> broadcastState = ctx.getBroadcastState(broadcastDescriptor);
            }

            // 4.1.2.广播获取新数据后，广播最新的数据
            processBroadcastElement(final IN2 value, final Context ctx, final Collector<OUT> out){
                // 更新广播元素
                ctx.getBroadcastState(broadcastDescriptor).put(KEY_BROADCAST, config)
            }
```

## 广播变量
- 具体事例参见 [凯新的技术社区-广播变量应用案例实战-广播变量](https://juejin.im/post/5bfa50615188255e9b61b323#heading-5)

``` java
// 输入流
DataSource<String> originStream= ....
// 1.需要广播的数据 
DataSet<Tuple2<String, Integer>> broadcastSet= ...
// 3.各环节中，需要读取广播变量，均需继承 Rich.* 类
data.map(new RichMapFunction<String, String>() {
    @Override
    public void open(Configuration parameters) throws Exception {

        // 4.获取广播变量
        getRuntimeContext().getBroadcastVariable("broadCastMapName");
    }
})
    // 2.将变量以 broadCastMapName 进行广播
    .withBroadcastSet(broadcastSet, "broadCastMapName");
```

# 异常
- Broadcast variables can only be used in DataSet programs
    - 广播元素与广播变量混淆
        - `getRuntimeContext().getBroadcastVariable(KEY_BROADCAST)`


# 参考
## 官方文档
- [The Broadcast State Pattern](https://ci.apache.org/projects/flink/flink-docs-release-1.7/dev/stream/state/broadcast_state.html#important-considerations)

## 代码示例
- [A Practical Guide to Broadcast State in Apache Flink](https://www.ververica.com/blog/a-practical-guide-to-broadcast-state-in-apache-flink)
- [Flink Broadcast 广播变量应用案例实战](https://juejin.im/post/5bfa50615188255e9b61b323)
- [聊聊flink的Broadcast State](https://juejin.im/post/5c234f93e51d450d503059bf)
- [yqjdcyy/Hello_Flink/array-broadcast-sink](https://github.com/yqjdcyy/Hello_Flink/tree/master/hello-flink/array-broadcast-sink)