---
title: "Redis.集群"
date: "2018-12-07"
categories:
 - "整理"
tags:
 - "Redis"
toc: true
---

# 描述
- 通过在多个 Redis 节点间共享数据
- 不支持同时处理多个键的命令
    - 避免高负载情况下的不可预知异常
- 支持 `CAP` 中的 `AP`

# 原理
## 数据分片

- 使用哈希槽算法，将键分发到对应槽位
    - 槽位= `CRC16(key)% 2^14`
    - 节点= 槽位所绑定的节点

- 结点操作
    - 删除
        - 将归属于该节点的槽位分发给剩余节点
        - 删除未关联槽位的节点
    - 新增
        - 添加新节点
        - 将各节点下的槽位抽取、指向新节点

- 结构
    - ![REDIS-CLUSTER-SLOT.png](http://doc.yqjdcyy.com/aa127d05-2e30-4fff-ac85-b11eb35b7770.png)


- 优点
    - 便于集群做线性扩展及平滑地扩、收容

## 主从复制
- 详见 [Redis.复制](/Redis.复制/)

# 工具

## redis-trib.rb

- 命令

    |     命令     |          参数         |                                            描述                                           |
    |:--------------:|:-----------------------|-------------------------------------------------------------------------------------------|
    | `create`     |                       | 创建集群                                                                                  |
    |              | `(host:port)*n`       | 配置集群中redis服务列表<br>**必填**<br>host:port                                            |
    |              | `--replicas<number>`  | 配置复制服务数，即一个master配置多少个slave                                               |
    | `check`      |                       |                                                                                           |
    |              | `host:port`           | 集群中任意节点信息<br>**必填**                                                            |
    | `info`       |                       | 查看集群信息                                                                              |
    |             | `host:port`           | 集群中任意节点信息<br>**必填**                                                            |
    | `fix`        |                       | 修复集群<br>1.存在牌迁移中的slot节点<br>2.slot未完全分配                                  |
    |              | `host:port`           |                                                                                           |
    |              | `--timeout<arg>`      |                                                                                           |
    | `reshard`    |                       | 在线迁移slot                                                                              |
    |              | `host:port`           | 集群中任意节点信息<br>**必填**                                                            |
    |              | `--from<arg>`         | 迁移slot的来源master节点<br>可为指定节点的nodeid,并以逗号隔开<br>或`--fromall`            |
    |              | `--to<arg>`           | 迁移至的目标master节点<br>**必填**                                                        |
    |              | `--slots<arg>`        | 迁移的slot数量<br>**必填**                                                                |
    |              | `--yes`               | 配置于rehash前需确认才能执行                                                              |
    |              | `--timeout<arg>`      | 配置migrate命令的超时时长                                                                 |
    |              | `--pipeline<arg>`     | 一次取出键值的数量<br>默认为10                                                            |
    | `rebalance`  |                       | 平衡集群节点slot数量                                                                      |
    |              | `host:port`           | 集群中任意节点信息<br>**必填**                                                            |
    |              | `--weight<arg>`       | 配置节点的权重<br>默认为1                                                                 |
    |              | `--threshold<arg>`    | 迁移slot数过多而需执行rebalance的阀值                                                     |
    |              | `--use-empty-masters` | 允许未分配slot的master节点参与分配                                                        |
    |              | `--timeout<arg>`      | 配置migrate命令的超时时长                                                                 |
    |              | `--simulate`          | 设置后，将只**模拟**，不执行                                                              |
    |              | `--pipeline<arg>`     | 一次取出键值的数量<br>默认为10                                                            |
    | `add-node`   |                       | 添加新节点                                                                                |
    |              | `new.(host:port)`     | 新增节点信息<br>**必填**                                                                  |
    |              | `old.(host:port)`     | 集群中任意节点信息<br>**必填**                                                            |
    |              | `--slave`             | 添加的节点是否作为slave节点<br>不设置时，则添加为master节点                               |
    |              | `--master-id<arg>`    | 仅当设置`--slave`时有效<br>指定从属的master节点<br>若未指定，则抽取子节点最小的master节点 |
    | `del-node`   |                       | 删除节点                                                                                  |
    |              | `host:port`           | 集群中任意节点信息<br>**必填**                                                            |
    |              | `nodeid`              | 待删除的节点ID<br>需不包含slot才可删除                                                    |
    | `set-timeout` |                       | 设置集群节点间心跳连接的超时时间                                                          |
    |              | `host:port`           | 集群中任意节点信息<br>**必填**                                                            |
    |              | `milliseconds`        | 默认为15000ms                                                                             |
    | `call`       |                       | 在集群全部节点上执行命令                                                                  |
    |              | `host:port`           | 集群中任意节点信息<br>**必填**                                                            |
    |              | `commandarg...`       | 待执行的命令                                                                              |
    | `import`     |                       | 将外部redis数据导入集群                                                                   |
    |              | `host:port`           |                                                                                           |
    |              | `--from<arg>`         |                                                                                           |
    |              | `--copy`              | 若本次执行启用该参数，失败重新执行前，建议清空集群优先                                    |
    |              | `--replace`           |                                                                                           |

- 示例

    - 创建集群，且每个 master 配有一个 slave

        ```c
        ruby redis-trib.rb 
            create 
            --replicas 1 
            10.180.157.199:6379 10.180.157.200:6379 10.180.157.201:6379 10.180.157.202:6379  10.180.157.205:6379  10.180.157.208:6379
        ```

    - 检查集群运行状况

        ```c
        ruby redis-trib.rb check 10.180.157.199:6379

        >>> Performing Cluster Check (using node 10.180.157.199:6379)
        M: b2506515b38e6bbd3034d540599f4cd2a5279ad1 10.180.157.199:6379
            slots:0-5460 (5461 slots) master
            1 additional replica(s)

        ...
        [OK] All nodes agree about slots configuration.
        >>> Check for open slots...
        >>> Check slots coverage...
        [OK] All 16384 slots covered.    
        ```
    - 从所有现有节点中，移 11 个槽位给 `80b661ecca260c89e3d8ea9b98f77edaeef43dcd` 节点

        ```c
        ruby redis-trib.rb reshard 
            --from all 
            --to 80b661ecca260c89e3d8ea9b98f77edaeef43dcd 
            --slots 11 
            10.180.157.199:6379
        ```
    - 进行槽位的模拟重新分配，其中针对 `b31e3a2e` 和 `b31e3a2e` 侧重性分配

        ```c
        ruby redis-trib.rb rebalance 
            --threshold 1 
            --weight b31e3a2e=5 --weight 60b8e3a1=5 
            --use-empty-masters  
            --simulate 
            10.180.157.199:6379
        ```

## cluster

- 命令

    | 类别 | 命令               | 参数        | 描述                                                                                                                                           |
    |:------:|:--------------------:|-------------|------------------------------------------------------------------------------------------------------------------------------------------------|
    | 集群 |                    |             |                                                                                                                                                |
    |      | `INFO`             |             | 显示集群信息                                                                                                                                   |
    |      | `NODES`            |             | 罗列集群已知节点，及其相关信息                                                                                                                  |
    | 节点 |                    |             |                                                                                                                                                |
    |      | `MEET`             |             | 将指定节点添加至集群                                                                                                                           |
    |      |                    | `<host:port>` | 待添加节点的地址和端口<br>**必填**                                                                                                             |
    |      | `FORGET`           |             | 从集群中移除指定节点                                                                                                                           |
    |      |                    | `<nodeid>`  | 节点id                                                                                                                                         |
    |      | `REPLICAS`         |             | 将当前节点设置为指定节点的从节点                                                                                                               |
    |      |                    | `<nodeid>`  | 节点id                                                                                                                                         |
    |      | `SAVECONFIG`       |             | 将该节点的配置文件保存至硬盘                                                                                                                   |
    |      | `SLAVES`           |             | 返回master节点的slave列表                                                                                                                      |
    |      |                    | `<nodeid>`  | master节点id                                                                                                                                   |
    |      | `SET-CONFIG-EPOCH` |             | 指定新节点以特定的配置时间<br>较高配置时间值，分配槽位时具有优先权<bR>要求当前配置时间为零且无slave                                             |
    |      | `SLOTS`            |             | 返回当前节点所分配的槽位                                                                                                                       |
    |      | `REPLICATE`        |             | 将当前节点配置为指定节点的从节点                                                                                                               |
    |      |                    | `<nodeid>`  | 指定主节点ID                                                                                                                                   |
    |      | `RESET`            |             | 重置节点<br>重置槽位映射                                                                                                                       |
    |      |                    | `<status>`  | HARD:生成新的节点ID<br>SOFT:默认项，清空节点信息，但不修改节点ID                                                                                 |
    |      | `FAILOVER`         |             | 强制从节点进行主节点的故障转移                                                                                                                 |
    |      |                    | `<option>`  | FORCE:不再与从节点进行沟通，忙进行故障转移<br>TAKEOVER:仅当所有master服务器关闭或分区，用于大量slave服务器的升级、切换                            |
    | 槽位 |                    |             |                                                                                                                                                |
    |      | `ADDSLOTS`         |             | 将指定槽位添加至当前节点                                                                                                                       |
    |      |                    | `<slot>+`   | 槽位值列表，以空格间隔<br>分配槽位均需处于未分配状态                                                                                            |
    |      | `DELSLOTS`         |             | 当前节点不再负责指定槽位                                                                                                                       |
    |      |                    | `<slot>+`   | 槽位值列表，以空格间隔                                                                                                                          |
    |      | `SETSLOT`          |             | 对指定槽位的指派、迁移、导入和取消                                                                                                               |
    |      |                    | `<slot>`    | 指定操作的槽位<br>**必填**                                                                                                                     |
    |      |                    | `<command>` | node:将槽位绑定到指定节点<br>migrating:将本节点的槽位迁移至指定节点<br>importing:将指定节点的槽位迁移至本节点<br>stable:取消对槽位的迁移和导入 |
    |      |                    | `[<nodeid]` | 非stable情况下**必填**                                                                                                                         |
    | 键值 |                    |             |                                                                                                                                                |
    |      | `KEYSLOT`          |             | 计算键值分配的槽位                                                                                                                             |
    |      |                    | `<key>`     |                                                                                                                                                |
    |      | `COUNTKEYSINSLOT`  |             | 返回槽位所包含的键值对数量                                                                                                                     |
    |      |                    | `<slot>`    |                                                                                                                                                |
    |      | `GETKEYSINSLOT`    |             | 返回当前节点指定槽位的指定数量键值对                                                                                                           |
    |      |                    | `<slot>`    |                                                                                                                                                |
    |      |                    | `<count>`   |                                                                                                                                                |

- 示例

    - 查看集群运行状态

        ```c
        redis-cli -c -p 7000
        cluster info

        cluster_state:ok
        cluster_slots_assigned:16384    // 已分配槽位数
        cluster_slots_ok:16384
        cluster_slots_pfail:0 
        cluster_slots_fail:0
        cluster_known_nodes:9           // 集群中节点总数
        cluster_size:4                  // 集群中主节点数
        cluster_current_epoch:9         // 当前集群时间变量值
        cluster_my_epoch:1              // 当前节点分配时间变量值
        cluster_stats_messages_sent:41417           // 该节点二进制总线所发送消息总数
        cluster_stats_messages_received:41417       // 该节点二进制总线所接收消息总数
        ```

    - 查看集群内所有节点的信息

        ```c
        cluster nodes

        8916fb224bbae3dc0291ca47e066dca0a62fba19 192.168.33.13:7004 slave 3d2b7dccfc45ae2eb7aeb9e0bf001b0ac8f7b3da 0 1446115185933 5 connected
        35bdcb51ceeff00f9cc608fa1b4364943c7c07ce 192.168.33.13:7003 master - 0 1446115184426 4 connected 12288-16383

        8916fb224bbae3dc0291ca47e066dca0a62fba19    // 节点 ID
        192.168.33.13:7004                          // host:port
        slave                                       // 节点类型「master|mysql|slave]
        3d2b7dccfc45ae2eb7aeb9e0bf001b0ac8f7b3da    // 从属主节点 ID    
        0                                           // PING发送的毫秒形式 Unix 时间，0为不进行 PING 操作
        1446115185933                               // PONG 接收的毫秒形式 Unix 时间戳
        5                                           // 配置的 epoch 值
        connected                                   // 连接状态
        12288-16383                                 // 12288-16383
        ```

    - 将 `6f5cd78ee644c1df9756fc11b3595403f51216cc` 节点的槽位 `16383` 分配给当前节点

        ```c
        cluster setslot 16383 importing 6f5cd78ee644c1df9756fc11b3595403f51216cc
        ```


# 拓扑

- 特点
    - 无中心结构
        - 所有节点互通
        - 通过 PING-PONG 机制实现
        - 内部使用二进制协议优化
    - 访问任意节点即可，内部可通过集群工具自动转向
        - `Redirected to slot [12182] located at 127.0.0.1:7002`
    - 拓扑的结构由 redis-cluster 进行维护
        - 将物理节点映射到槽位上
        - 维护 `value/slot/node` 的三方联系
    - 上限
        - 2^14
        - 16384

- 结构
    - ![REDIS-CLUSTER-TOPOLOGY.png](http://doc.yqjdcyy.com/80077a8f-aa14-4e2d-9e4e-b836b6752e95.png)


# 补充
## 一致性哈希

- 算法
    - 将节点与键值，都通过 Hash 计算后，映射到圆环数组上
        - 节点所在槽位至上一节点之间的槽位上的键，均分发到此节点
    - 节点删除或新增不用做特别处理
        - Hash 匹配到槽位后，往后一直找到节点槽位时停止

- 实现
    - Chord 算法
    - KAD 算法

- 上限
    - 2^32

- 结构
    - ![REDIS-CLUSTER-HASH-CONSISTENT.png](http://doc.yqjdcyy.com/2864a6af-24c3-41ef-992f-804f41f3c9f7.png)

## 哈希算法评判标准

| 特性                   | 描述                                                                             |
|----------------------|--------------------------------------------------------------------------------|
| 平衡性<br>Balance      | 哈希结果尽可能均匀地分发到所有缓冲中                                             |
| 单调性<br>Monotonicity | 重复哈希值情况下，避免或解决槽位冲突                                              |
| 分散性<br>Spread       | 相同的内容，被不同的终端映射到不同的缓冲区<br>节点感知其它节点数等导致，应尽量避免 |
| 负载<br>Load           | 针对特定缓冲区，因不同内容被映射而至<br>结构分散性的情况，应尽量避免               |

## 升级|故障转移

- 选举条件
    - 主节点异常
    - 该节点所有槽位
    - 从节点复制链接的断线时长，少于节点时间限制* `REDIS_CLUSTER_SLAVE_VALIDITY_MULT `

- 升级条件
    - 申请从节点的所属主节点已宕机
    - 所有满足条件的从节点中，节点 ID 排序最小
    - 申请从节点运行状态正常

- 升级方式
    - 由符合条件的从节点向其它主节点发送授权请求

- 升级|故障转移
    - 其它从节点转向该节点进行复制
    - PONG 方式告知其它节点
        - 当前节点为主节点
        - 当前节点为已升级的从节点
        - 显式广播，加速节点识别
    - 接管原主节点所属的哈希槽
    - 将已下载的主节点标识为 `PROMOTED`
        - 重新上线后，将转换为现任主节点的从节点


# 参考
- [Redis 集群教程](http://www.redis.cn/topics/cluster-tutorial.html)
- [一致性hash算法简介](http://blog.huanghao.me/?p=14)
- [五分钟理解一致性哈希算法](https://blog.csdn.net/cywosp/article/details/23397179)
- [Redis集群特点](https://blog.csdn.net/z15732621582/article/details/79121213)
- [redis集群 数据迁移方式 Hash槽 和 一致性hash对比，优缺点比较](https://blog.csdn.net/tianpeng341204/article/details/78963850)
- [Chord](https://en.wikipedia.org/wiki/Chord_(peer-to-peer))
- [Kademlia](https://zh.wikipedia.org/zh-hans/Kademlia)
- [redis cluster管理工具redis-trib.rb详解](http://weizijun.cn/2016/01/08/redis%20cluster%E7%AE%A1%E7%90%86%E5%B7%A5%E5%85%B7redis-trib-rb%E8%AF%A6%E8%A7%A3/)
- [CLUSTER.*](https://redis.io/commands)
- [Redis集群节点超时时限](https://blog.csdn.net/yaomingyang/article/details/79081299)
- [集合 | Cluster](https://cloud.tencent.com/developer/chapter/16266)
- [redis cluster配置文件和集群状态详解](http://blog.51cto.com/lookingdream/1933049)
- [Redis 3.0.5 集群的命令、使用、维护](https://www.zybuluo.com/phper/note/205009)
- [Redis Cluster部署、管理和测试](http://www.cnblogs.com/zhoujinyi/p/6477133.html)