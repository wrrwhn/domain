+++
date = "2016-12-10T22:07:46+08:00"
title = "Redis.Basic"
draft = false
tags = ["整理","Redis"]
share = true
+++


## 参考
- [Redis Virtual Memory: the story and the code](http://oldblog.antirez.com/post/redis-virtual-memory-story.html)
- ​[Virtual Memory technical specification](http://redis.io/topics/internals-vm)

## 数据类型
### Keys
- del key1 key2... keyN
    + 删除给定KEY
- type key
    + 返回当前key类型
- keys pattern
    + 匹配指定模式的所有KEY
- randomkey
    + 返回当前库随机KEY
- rename oldkey newkey
    + 重命名，若newkey存在则以oldkey值覆盖
- renamenx oldkey newkey
    + 重命名，但newkey存在时返回0
- dbsize
    + 返回当前数据库key数量
- expire key seconds
    + 为KEY指定过期时间
- ttl key
    + 返回设置过期时间的KEY的剩余时间
- select db-index
    + 通过索引选择数据库，默认为0，默认数据库个数16个
- move key db-index
    + 将当前库中的KEY移动至指定索引库中
- flushdb
    + 删除当前库中所有KEY，不会失败
- flushall
    + 删除所有库中的所有KEY，不会失败

### String
#### 特点
- 最基本类型，二进制安全，可包含任何数据，上限为一G字节
#### 常用方法
- set key value
    + 设置KEY对应的Sring类型值
- setnx key value
    + 若key不存在时设置，nx= not exists
- get key
    + 获取KEY对应Sring值
- getset key value
    + 原子设置KEY值，并返回原值
- mset key1 value1... keyN valueN
    + 批量写入
- msetnx key1 value1... keyN valueN
    + 批量写入，但不覆盖已有值
- mget key1 key2... keyN
    + 一次性获取多个KEY
- incr key
    + key++
- decr key
    + key--
- incrby key integer
    + set key nvl(key, 0)+integer
- decrby key integer
- substr key start end
    + 截取KEY的值，只读不改，且从0开始 

### List
#### 特点
- 元素均为String类型的双向链接
- [lr]push/[lr]pop/llen的时间复杂度均为O(1)
- 最大长度为2的32次方-1
#### 常用方法
- l/rpush key string
    + 于key列表头部[尾部]添加元素
- llen key
    + 返回key列表长度
- lrange key start end
    + 返回key列表指定区间内的元素
- lset key index value
    + 设置key列表指定位置的元素值
- lrem key count value
    + 从左开始key列表中指定count个值为value的元素，若count=0表示清除所有
- l/rpop key
    + 于key列表的头部[尾部]移除元素
- blpop key1... keyN timeout
    + 从左至右扫描并返回第一个非空list进行lpop操作，若均为空或不存在，则阻塞timeout时间（若timeout=0则表示一直等待），并且于阻塞期间，若有记录插入其中一个列表，则立即对其执行lpop操作
- rpoplpush strkey destkey
    + 将strkey的尾部移除并插入destkey的头部，该操作为原子操作

### Set
#### 特点
- string类型无序集合，最大可含2的32次方-1个元素
- 增删查因为hash table实现，复杂度均为O(1)
- 调整hash table大小需同步，会获取锁而阻塞其它读写操作。
#### 常用方法
- sadd key member
    + 为数据添加元素
- stem key member
    + 移除指定数组中的指定元素
- spop key
    + 随机取除任一元素
- srandmember key
    + 随机读取任一元素
- smove strkey dstkey member
    + 将strkey数组中的member移动至dstkey数组中，原子操作。
- scard key
    + 返回数组元素个数
- sismember key member
    + 判断member是否为key数组的元素
- sinter key1..keyN
    + 返回数组的交集
- sinterstore dstkey key1..keyN
    + 存储sinter结果至dstkey
- sunion key1..keyN
    + 所有数组的并集集
- sunionstore dstkey key1..keyN
    + 存储sunion结果至dstkey
- sdiff key key1..keyN
    + 返回key中于其它数组中所不包含的元素
- sdfif dstkey key key1..keyN
    + 存储sdiff结果至dstkey
- smembers key
    + 返回key中所有元素

### Sorted Set
#### 特点
- [score: double, value: string]
- score映射至hash table，因此获取score的复杂度为O(1)
- 增删开销为O(log(N))
- 一般作为索引使用
#### 常用方法
- zadd key score member
    + 添加元素到集合，若element存在则更新其score
- zrem key member
    + 移除集合中的element元素
- zincryby key incr member
    + 为集合中element元素的的score加上incr值，并调整list序列顺序
- z[rev]rank key member
    + 显示集合中member元素的排位
- z[rev]range key start end
    + 显示集合中指定区域的数值情况
- zcount key min max
    + 显示集中指定区域内的元素个数
- zcard key
    + 显示集合中无数的个数
- zscore key element
    + 显示集中中element元素对应的score值
- zremrangebyrank[score] key min max
    + 按排名[score]删除集合中给定区域的值

### Hash
#### 特点
- field/value映射表，适合存储对象，复杂度为平均O(1)
- 初始使用zipmap存储，待达到配置文件指定限制后转hash实现
#### 常用方法
- hset key field value
- hget key field
- hmset key field1 value1..fieldN valueN
- hmget key field1..fieldN
- hexists key field
- hdel key field
- hlen/hkeys/hvals/hgetall key
    + 返回hash中的field数量/所有field/所有value/所有的field和value

## 排序
### 特点
- 支持对list/set/sorted set进行排序操作
### 格式
- `SORT key [BY pattern] [LIMIT start count] [GET pattern] [ASC|DESC] [ALPHA] [STORE dstkey]`
### 详解
 - `[BY pattern]`
    + 将集合元素内容按给定pattern组成新key后，按其对应的内容进行排序。
    + 例
        - `list uid= 1/2/3/4`
        - `set user_name_1[2/3/4]= admin[jack/peter/mary]`
        - `set user_level_1[2/3/4]= 99[32/80/9]`
        - `sort uid by user_name_*= 4/3/2/1`
        - `sort uid by user_level_*= 4/2/3/1`
 - `[LIMIT start count]`
    - 限制返回由start开始的count个结果
 - `[GET pattern]`
    + 返回pattern作为新值时的元素值。
    + 例
        * `sort uid get user_name*= nil/nil/nil/nil`
        * `sort uid get user_name_*= admin/jack/peter/mary`
 - `[ASC|DESC]`
    + 指定排序方式，如bz/az/1/2或2/1/bz/az(desc)
 - `[ALPHA]`
    + 按字母进行排序，如1/2/az/bc或bz/az/2/1(desc)
 - `[STORE dstkey]`
    + 将结果以list方式保存
 - `[KEY->FIELD]``
     + 于BY/GET pattern方式中使用，用于获取pattern组装值对应的域值
     + 例
         * `hmset user_info_1 name admin level 99`
         * `hmset user_info_2 name jack level 10`
         * `hmset user_info_3 name peter level 25`
         * `hmset user_info_4 name mary level 70`
             - `sort uid user_info_*`
             - -> `desc get user_info_*`
             - ->name
                + 按user_info_*中的等级进行降序排列，并输出对应位置的名称

## 事务
### 特点
- 单线程来处理所有client请求
- 一般接到client发来的命令时立即处理并返回处理结果，但连接中发出multi命令后进入事务上下文，后续命令先放入队列中，并于接收到exec命令后执行所有并汇总打包所有 命令结果至client
- 事务中的写操作不能依赖于事务中的读操作结果，因为事务中命令只排队不立即执行

### 缺陷
- 事务中一个命令失败，并不回滚其它命令
- 事务执行期间，redis挂了则后面事务丢失。
    + -> 使用append-only file方式持久化，redis会使用单个write操作定稿事务内容，但仍存在部分写事务到磁盘。
    + 而redis重启时会检测部分写入事务情况，并失败退出。
    + 可用redis-chec-aof进行修复，删除部分写入的事务内容后重启即可。

### 常用指令
- multi
    + 开启事务上下文
- exec/discard
    + 执行或放弃操作，结束事务上下文
- watch
    + 整个连接内监听keys，可于事务exec前判断是否有keys值发生改变，而决定跳出事务。

## pipeline
### 介绍
- redis为cs模式的tcp server，使用类http请求响应协议。
- 支持一个client通过一个socket连接必起狐假虎威请求命令，并阻塞等待redis服务处理。
- 由于网络延迟等，客户端一秒可能只能发N条命令，但redis服务一秒可以执行远大N条的命令。
- 而pipeline便是将命令打包后一并发出一并处理的操作。
### 注意事项
- pipeline方式打包指令，redis须于处理完所有命令前无缓存所有命令处理结果，命令越多则缓存消耗内存越大。

### 代码
```
ConnectionSpec spec = DefaultConnectionSpec.newSpec("192.168.56.55", 6379, 0, null);
JRedis jredis = new JRedisPipelineService(spec);
```

## 持久化
### 特点
- 支持持久化的内存数据库
### 类型
#### Snapshooting（快照）
- 默认方式
- 特点
    + 将内存数据以快照方式写入二进制文件，默认文件名为dump.rdb
- 配置
    + `redis.conf-> save time count`，若time秒内count个key被修改则发起快照保存
- 流程
    + redis调用fork，生成父子进程
    + 父进程继续处理client请求，子进程负责将内存内容写入到临时文件。
        * 由于os的写时复制机制(copy on write)父子进程会共享相同的物理页面。
        * 当父进程处理写处理写请求时os会为父进程要修改的页面创建副本，而不是写共享页面。
        * 所以子进程的地址空间内的数据是fork时刻整个数据库的一个快照。
    + 当子进程将快照写入临时文件完毕后，用临时文件替换原来的快照文件，然后子进程退出。
- 注意
    + client可使用save/bgsave通知redis做一次快照持久化。
    + save为主线程中保存快照，会阻塞所有client请求。
    + 每次快照持久化均将内存数据完整写入，数据量大会影响IO性能。
    + 由于快照一段时间操作一次，若redis意外down掉，会丢失最后一次快照后的所有修改。

#### Append-only fill（aof）
- 特点
    + redis会将每一个收到的写命令都通过write函数追加至文件中，默认为appendonly.aof。redis重启时再重新执行文件命令来重建事个数据库。
- 配置
    + redis.conf
    ```
    appendonly yes    //启用aof持久化方式
    #appendfsync always    //每次收到写命令就立即强制写入硬盘，最慢量最能保证完全持久化，不推荐
    appendfsync everysec    //每秒钟强制写入磁盘一次，推荐使用
    #appendfsync no            //完全依赖OS，性能最好，但无法保证持久化
    ```
- 流程
    + 提供bgrewriteaof命令，要求redis使用快照方式将内存数据以命令方式保存至临时文件，最后替换原文件。
    + redis调用fork，生成父子两个进程
    + 子进程根据内存中的数据库快照，往临时文件中写入重建数据库状态的命令
    + 父进程继续处理client请求，将写命令写入aof文件并缓存起来（避免子进程重写失败）
    + 子进程将快照内容写入命令方式的临时文件后，通知父进程将缓存写命令也写入
    + 父进程将临时文件远的旧aof文件并重命令，同时继续往之追加写命令
- 注意
    + 只根据现有库记录写命令，不读旧AOF，相当于快照方式


## 主从复制
### 特点
- 主从复制允许多个slave server拥有和master server相同的数据库脚本。
- slave可以和master或其它slave相连。
- 主从复制不会阻塞master，而slave在初次同步时会阻塞。因此可通过多个slave专用于处理client的读请求以提高系统的可伸缩性。同时可限制master的数据持久化而转于slave上配置数据持久化。
### 过程
- 配置好slave服务器后，slave自动建立与master的连接并发送sync命令。
- master为slave创建后台进程，保存快照并缓存后续的写命令。
- 待快照完成后由master发送给slave，slave保存至本地后加载至内存恢复。后续master再将缓存写命令同样连接发送给slave。
- slave和master连接断开时时自动重连，而多个slave同步连接时，仅会启动一个进程来写数据库镜像并发送给所有slave。
### 配置
- `slaveof 192.168.1.1 6379`
    + 指定master的ip和端口

## 虚拟内存
### 特点
- 暂时将不经常访问的数据从内存交换到磁盘
- 不使用OS虚拟内存原因
    + redis.obj远小于虚拟内存的最小单位4K，影响系统识别页面活跃情况而不利于资源回收
    + redis可将交换到磁盘的对象压缩（去除指针和对象元数据信息），比内存对象小10倍
### 配置
```
vm-enabled yes #开启vm功能
vm-swap-file /tmp/redis.swap #交换出来的value保存的文件路径/tmp/redis.swap
vm-max-memory 1000000 #redis使用的最大内存上限，超过上限后redis开始交换value到磁盘文件中。
vm-page-size 32 #每个页面的大小32个字节
vm-pages 134217728 #最多使用在文件中使用多少页面,交换文件的大小= vm-page-size * vm-pages
vm-max-threads 4 #用于执行value对象换入换出的工作线程数量。0表示不使用工作线程
```
