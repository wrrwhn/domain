---
title: "Redis.持久化"
date: "2018-11-25"
categories:
 - "整理"
tags:
 - "Redis"
toc: true
---

# 持久化
## RDB

### 形式
- 运行时，RDB 程序将内存数据快照保存至磁盘中，重启时载入 RDB 文件以不愿数据库状态
- 针对超过20字节文本，会使用 LZF 编码形式进行压缩

### 原理
- COW
    - copy on write
    - linux.kernel 上支持
    - 多调用方请求相同资源时，将获得指向相同资源的同一指针；直到某一调用方意图修改时，将针对其复制一份资源出来，单独进行修改；而这对其它调用方不可见
        - 保障线程案例，父进程的修改，不会影响到子进程的数量
        - 类似于 java 的 string 对象

### 时机
- 由系统定时任务 `serverCron` 周期性检查是否满足条件，以调用 `bgsave`
    - 条件为 `config get save` 中所列项
- 而 `save` 操作将由用户手动触发

### 保存
- SAVE
    - 调用 `rdbSave`，**阻塞** `redis` 主线程
        - 保存完毕之前，服务器无法处理请求
        - 需先检查 `BGSAVE` 是否正在执行，否则报错以告知
- BGSAVE
    - `fork` 一个 **子进程** 来调用 `rdbSave`
        - 待保存完毕后，向主进程发送信号

### 载入
- rdbLoad
    - 启动时执行，读取 RDB 文件并载入内存中
        - 每载入 **1000** 个键，就处理一次所有已到达的请求
        - 期间仅如下指令可被处理
             - PUBLISH
             - SUBSCRIBE
             - PSUBSCRIBE
             - UNSUBSCRIBE
             - PUNSUBSCRIBE
    - 若服务启动时，打开 `AOF` 功能，则优先使用 `AOF` 文件来还原

### 文件结构

- 文件结构

    | REDIS   | RDB-VERSION                            | SELECT-DB          | KEY-VALUE-PAIRS | EOF                                            | CHECK-SUM                                               |
    |---------|----------------------------------------|--------------------|-----------------|------------------------------------------------|---------------------------------------------------------|
    | "REDIS" | 四字节 RDB 版本号<br>如当前版本为 0006 | 数据所属数据库号码 | 键值对数据      | 内容结尾<br> rdb.h/EDIS_RDB_OPCODE_EOF<br> 255 | 内容校验和<br> uint_64t <br> 为0 时表示已关闭校验和功能 |


- 键值对数据

    | OPTIONAL-EXPIRE-TIME                             | TYPE-OF-VALUE             | KEY                                | VALUE                                                   |
    |--------------------------------------------------|---------------------------|------------------------------------|---------------------------------------------------------|
    | 有设置过期时间时出现<br>单位为毫秒的 UNIX 时间戳 | 编码方式                  | 数据类型                           | 存储值                                                  |
    |                                                  | REDIS_ENCODING_INT        | REDIS_STRING                       | <=32bit<br>整数形式<br> 00001000= 8                     |
    |                                                  |                           |                                    | >32bit<br>  LEN+CONTENT                                 |
    |                                                  | REDIS_ENCODING_RAW        | REDIS_STRING                       | 8/16/32 bit INT<br> 整数形式                            |
    |                                                  |                           |                                    | <20bit <br> 20bit 空间存储                              |
    |                                                  |                           |                                    | >20bit <br> `LZF`-FLAG+ COMPRESSED-LEN+ COMPRESSED-CONTENT |
    |                                                  | REDIS_ENCODING_LINKEDLIST | REDIS_LIST                         | NODE-SIZE+ (NODE-VALUE)* NODE-SIZE                      |
    |                                                  | REDIS_ENCODING_HT         | REDIS_SET                          | SET-SIZE+ (ELEMENT)* SET-SIZE                           |
    |                                                  | REDIS_ENCODING_SKIPLIST   | REDIS_ZSET                         | ELEMENT-SIZE+ (MEB+ SCORE)* ELEMENT-SIZE                |
    |                                                  | REDIS_ENCODING_HT         | REDIS_HASH                         | HASH-SIZE+ (KEY+ VALUE)* HASH-SIZE                      |
    |                                                  | REDIS_ENCODING_ZIPLIST    | REDIS_LIST/ REDIS_HASH/ REDIS_ZSET | LEN+ ZIPLIST                                            |
    |                                                  | REDIS_ENCODING_INTSET     | REDIS_SET                          | LEN+ INTSET                                             |

### 补充
- 是否进行 LZF 压缩，由配置参数 `rdbcompression` 决定
    - 而该配置默认为 `yes`

## AOF
### 形式
- 以协议文本方式，存储所有对数据库写入的命令及其参数
    - 默认使用 `AOF_FSYNC_EVERYSEC` 策略
- 由服务器定时函数 `serverCron` 运行期间调用 `flushAppendOnlyFile|stopAppendOnly` 方法
    - 后者于 AOF 策略关闭时调用

### 机制
- 命令传播
    - 将执行完的命令及其参数，发送到 AOF 程序中
- 缓存追加
    - 将命令转化为 RESP 协议格式，并追加至服务器的 AOF 缓存中
- 文件写入和保护
    - 缓存区大小等满足条件后，将调用 `fsync` 或 `fdatasync` 函数，将缓存内容追加至 AOF 磁盘文件中

### 数据格式

- 符号含义

    | 字符 | 含义                               |
    |------|----------------------------------|
    | `+`  | 单行字符串                         |
    | `$`  | 多行字符串<br>符号后跟字符串的长度 |
    | `：`  | 数值<br>符号后跟整数的字符串形式   |
    | `-`  | 错误消息                           |
    | `*`  | 数组<br>符号后跟数组的长度         |

- 示例
    - 指令
        - `SET KEY VALUE`
        - `RPUSH list 1 2 3 4 RPOP list`
    - 协义内容
        - `*3\r\n$3\r\nSET\r\n$3\r\nKEY\r\n$5\r\nVALUE\r\n`
        - `*2\r\n$6\r\nSELECT\r\n$1\r\n0\r\n*6\r\n$5\r\nRPUSH\r\n$4\r\nlist\r\n$1\r\n1\r\n$1\r\n2\r\n$1\r\n3\r\n$1\r\n4\r\n*2\r\n$4\r\nRPOP`
            - `SELECT` 由 AOF 程序自行添加上去，保证所执行库正确


### 命令传播

- 操作
    - AOF 缓存
    - REPLICATION 缓存

- 流程
    - ![REDIS-AOF-BROADCAST.png](http://doc.yqjdcyy.com/9d4eb331-2d22-4517-ada3-d32cc7ebe9d8.png)


### 缓存追加

- 接收命令（命令+ 参数+ 使用数据库）信息
- 将命令行信息生成为协议文本
- 将协议文本追加至 `aof_buf` 
    - `aof_buf` 的结构为 `redis.h/redisServer`

### 保存

- 时机
    - 任务函数执行 | 事件处理器执行时，调用 `aof.c/flushAppendOnlyFile`
    - 具体的保存和写操作，视策略而定

- 操作
    - WRITE
        - 将 `aof_buf` 的缓存写入至 AOF 文件
    - SAVE
        - 调用 `fsync`| `fdatasync` 将 AOF 文件写入磁盘

- 模式

    | MODEL                                                          | WRITE                                                                                 | WRITE-BLOCK | SAVE                                                                                        | SAVE-BLOCK |
    |----------------------------------------------------------------|---------------------------------------------------------------------------------------|-------------|---------------------------------------------------------------------------------------------|------------|
    | `AOF_FSYNC_NO`<br>不保存<br>由`主进程`调用                     | 每次触发                                                                              | BLOCK       | 仅如下情况触发，会引起主进程阻塞<br>Redis关闭 <br>AOF功能关闭 <br>系统写缓存刷新（已满或定期） | BLOCK      |
    | `AOF_FSYNC_EVERYSEC`<br>每一秒钟保存一次<br>由后台`子进程`调用 | 如下情况触发<br>SAVE操作执行超过2秒，需等 Save 完成后阻塞进行<br> 此时未进行 SAVE 操作 | BLOCK       | 仅当此时未在执行 SAVE 操作，且完成已超过 1 秒                                                | `NO-BLOCK` |
    | `AOF_FSYNC_ALWAYS`<br>每执行一个命令保存一次<br>由`主进程`调用 | 阻塞式执行                                                                            | BLOCK       | 阻塞式执行                                                                                  | BLOCK      |

- 流程
    - ![REDIS-AOF-SAVE.png](http://doc.yqjdcyy.com/10b02eeb-7074-459c-b3b4-2de7c52b2ad9.png)


### 安全性

| 模式               | 停机时丢失的数据量                                   |
|--------------------|---------------------------------------------|
| AOF_FSYNC_NO       | 操作系统最后一次对 AOF 文件触发 SAVE 操作之后的数据。 |
| AOF_FSYNC_EVERYSEC | 一般情况下不超过 2 秒钟的数据。                       |
| AOF_FSYNC_ALWAYS   | 最多只丢失一个命令的数据。                            |


### 还原
- 流程
    - 创建不带网络连接的虚拟客户端
    - 读取 AOF 文本
        - 还原为命令、参数等信息
        - 使用虚拟客户端调用
    - 执行完成所有的命令


### 重写
- 目的
    - 解决由于频繁操作和时间过度，导致的 AOF 文件过大的问题

- 方案
    - 创建新的 AOF 以代替，优化和合并相关操作

- 针对类型
    - 列表
    - 集合
    - 字符串
    - 有序集
    - 哈希表

- 示例
    - 历史

        ```redis
        RPUSH list 1 2 3 4      // [1, 2, 3, 4]
        RPOP list               // [1, 2, 3]
        LPOP list               // [2, 3]
        LPUSH list 1            // [1, 2, 3]
        ```

    - 重写
        - `RPUSH list 1 2 3`

- 触发时机
    - 满足如下所有条件
        - 未在执行 `BGSAVE`
        - 未在执行 `BGREWRITEAOF`
        - AOF.SIZE> `server.aof_rewrite_min_size`
            - `server.aof_rewrite_min_size` 默认为 `1MB`
        - NOW.AOF.SIZE> `1+ aof_rewrite_perc`* OLD.AOF.SIZE
            - `aof_rewrite_perc` 默认为 **`100%`**


- 流程
    - ![REDIS-AOF-SAVE-REWRITE.png](http://doc.yqjdcyy.com/d8e5cab6-fce9-4348-b14a-c71a02cb3cd4.png)


## VM

- 虚拟内存方式
- 用户空间的数据换入换出的策略
- 实现效果差，代码复杂、重启慢、复制慢，已**弃用**

## Diskstore

- B-Tree 实现，**实验**阶段

# 指令
## 关闭|开启策略


# 主从复制

- 流程
    - ![REDIS_MASTER_SLAVE_COPY.png](http://doc.yqjdcyy.com/6a5d9e57-bd80-4600-9c80-e87d4fbbe524.png)

- 细节
    - `Slave` 发送 `sync` 命令后，会阻塞等待 `Master` 的内存快照
    - `Master`将于内存快照子进程结束后，将文件发送给 `Slave` 
    - `Slave` 接收到内存快照后，将保存至本地，清空内存表，并以此重建内存数据结构
    - 后续的数据更新，将通过与 `Slave` 连接的发送缓存队列，依次发送以便于 `Slave` 执行、同步

# 原理
## IO 函数
### Open

- 公式
    - `int open(const char path, ...int oflag );`

- 参数
    - `path`
        - 想要打开的文件路径名
    - `oflag`
        - 打开的方式，可多种同时支持，以「逗号」隔开
        - 可选项如下 

            | 模式     | 作用                      |
            |----------|-------------------------|
            | O_WRONLY | **只写** 模式打开         |
            | O_APPEND | 于文件结尾处**追加**内容  |
            | O_CREAT  | 文件不存在时，先行**创建** |

### Write

- 公式
    - `ssizet write(int fd, const void *buf, sizet nbytes);`

- 参数
    - `*buf`
        - 写入的数据
    - `nbytes`
        - 要写入数据的数量

- 注意
    - `nbytes` 与 `ssizet` 不一致时，说明写入异常

### Ftruncate

- 公式
    - `int ftruncate(int fd, off_t length);`

- 参数
    - `length`
        - 文件截取后的文件大小
            - 相较之前小，则超过 `length` 长度的数据将无法再访问
            - 相较之较大，则超出部分填充为 0


### Sync

#### 注意事项
- 延迟写
    - `Unix` 和 `Linux` 上的实现方式
    - 向文件写数据时
        - 优先复制到`缓冲`区
        - 而入排入`队列`
        - 最后再同步写入至`磁盘`

#### Sync
- 将 **所有** 数据复制到缓冲区，排入队列后立即返回
- `update` 的系统守护进程以`周期`地调用 `sync` 函数写入磁盘
    - 周期一般为 **30s**

#### FSync 
- 针对指定的单一文件生效，直至写入磁盘后结束
    - 针对 **数据库** 场景

### FDataSync
- 相较 `Fsync`，仅更新文件的数据部分
    - 文件的数据和信息（尺寸、访问时间、更新时间）分处于**不同文件**
    - 因此 `FDataSync` 仅需要进行**一次** IO 操作，而 `Fsync` 需要进行**两次** IO 操作



- 查询
    - `CONFIG GET (SAVE|APPENDFSYNC)`

- 开启|关闭
    - RDB
        - `CONFIG SET SAVE ""`
            - 关闭 RDB 功能
        - `CONFIG SET SAVE "900 1 300 10"`
            - 开启 RDB 功能
            - **900s** 内若至少有 **1次** 变更，或 **300s** 内至少 **10次** 变更，则调用 SAVE 命令
    - AOF
        - `CONFIG SET APPENDFSYNC NO`
            - 关闭 AOF 功能
        - `CONFIG SET APPENDFSYNC YES`
            - 开启 AOF 功能

# 参考
- [RDB](https://redisbook.readthedocs.io/en/latest/internal/rdb.html)
- [AOF](https://redisbook.readthedocs.io/en/latest/internal/aof.html)
- [关于Redis的aof持久化的二三事](https://segmentfault.com/a/1190000016096933)
- [Linux 内核的文件 Cache 管理机制介绍](https://www.ibm.com/developerworks/cn/linux/l-cache/index.html)
- [CONFIG SET parameter value](https://redis.io/commands/config-set)
- [Redis原理详解](http://blog.51cto.com/gudaoqing/1601114)
- [redis持久化——RDB、AOF](https://lanjingling.github.io/2015/11/16/redis-chijiuhua/)
- [redis原理](https://juejin.im/post/5b7cbbace51d4538850307dd)
- [redis原理](https://juejin.im/post/5b7cbbace51d4538850307dd)
- [fork()后copy on write的一些特性](https://zhoujianshi.github.io/articles/2017/fork()%E5%90%8Ecopy%20on%20write%E7%9A%84%E4%B8%80%E4%BA%9B%E7%89%B9%E6%80%A7/index.html)
