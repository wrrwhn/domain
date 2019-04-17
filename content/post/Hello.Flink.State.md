


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