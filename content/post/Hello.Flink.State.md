


Keyed State 
    KeyedStream
    only be used in functions and operators 
    exactly one state-partition per key
    <operator, key>
    Keyed State is further organized into so-called Key Groups.
        redistribute 
        atomic unit
        as many Key Groups as the defined maximum parallelism
        

Operator State.
    each operator state is bound to one parallel operator instance
    support redistributing state among parallel operator instances when the parallelism is changed.

Managed State
    Flink internal hash tables(RocksDB/ ValueState) -> encodes the states and writes them into the checkpoints.
    recommended
         able to automatically redistribute state when the parallelism is changed
        do better memory management.
    only be used on a KeyedStream
    type
        value
        list
        reducing
        aggregating
        folding
        map


Raw State
    keep in their own data structures -> write a sequence of bytes into the checkpoint
    only be used when implementing operators




State
    Keyed
        keyedStream 的状态
        对应每个 Key 有相对应的一个 state
    Operator
        各 Operator 对应的 state
        如 Source 读取 kafka 的偏移量等
    Broadcast

存储数据
    原始状态
        Raw State
        用户自行管理状态的数据结构 ，框架于 checkpoint 时使用 byte[] 读写状态内容
    托管状态
        ValueState
        ListState
        MapState        
        ReducingState<br>FoldingState        

存储方式
    |方式|场景|
    |-|-|
    |Memory|调试<br>无状态<br>可容忍数据丢失/重复场景|
    |FileSystem|普通状态<br>窗口<br>键值结构|
    |RocksDB|越大状态<br>越长窗口<br>大型键值结构|

优点
    ExactlyOnce
    增量+异地备份
    本地恢复


# 参考
- [学习 Flink（五）：状态](http://blog.dyingbleed.com/flink-5/)    
- [Apache Flink状态管理和容错机制介绍](https://www.iteblog.com/archives/2417.html)    
- [Flink流计算编程--状态与检查点](https://blog.csdn.net/lmalds/article/details/51982696)    
- []()    
- []()    


https://ci.apache.org/projects/flink/flink-docs-stable/dev/stream/state/state.html



{
    code:   code
    keys:   [col1, co2],
    time:   col,
    dict:   [{
            name:
            type:   
            operator:   
        }
    ]
}