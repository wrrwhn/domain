---
title: "Redis.事务"
date: "2018-11-25"
categories:
 - "整理"
tags:
 - "Redis"
toc: true
---

# 描述
- 服务器**异步接收**、存储所要执行的命令，最后**阻塞**、按**顺序**、**执行所有**的命令
- 事务状态下，服务器将客户端的请求请求存入事务队列后，即返回 `QUEUED` 告知已入队
    - 请求的命令信息包括命令、参数、参数个数

# 结构

|     结构    |   属性   |       类型       |       描述       |
|-------------|----------|------------------|------------------|
| redisClient |          |                  |                  |
|             | mstate   | multiState       | 事务状态         |
| multiState  |          |                  |                  |
|             | commands | multiCmd `*`     | 事务队列<br>FIFO |
|             | count    | int              | 命令数量         |
| multiCmd    |          |                  |                  |
|             | cmd      | redisCommand `*` | 命令指针         |
|             | argv     | robj `**`        | 参数             |
|             | argc     | int              | 参数个数         |

# 命令

| 命令    | 作用         | 描述                                                                                                                                                                                 |
|---------|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| MULTI   | 开启事务     | 已开启事务状态下，重复发送，仅通知操作有误，不影响事务的继续执行                                                                                                                        |
| DISCARD | 取消事务     | 清空事务队列<br>将客户端取消事务状态                                                                                                                                                 |
| EXEC    | 执行事务     | 服务器阻塞、顺序执行所有缓存命令，并返回所有命令的结果<br>清除命令缓存、`watch_keys` 中该客户端的相关记录                                                                               |
| WATCH   | 监测键值变化 | 于进入**事务状态前**执行<br>在 `EXEC` 调用时，若监测键有变化，则该事务不执行，直接返回失败<br>`WATCH` 值有变化时，将更新客户端的 `REDIS_DIRTY_CAS` 值<br>监测后，将一直持续到 `EXEC` 执行 |

# 示例

## 事务流程
```c
redis> MULTI
OK

redis> SET "name" "Practical Common Lisp"
QUEUED

redis> GET "name"
QUEUED

redis> SET "author" "Peter Seibel"
QUEUED

redis> GET "author"
QUEUED

redis> EXEC
1) OK
2) "Practical Common Lisp"
3) OK
4) "Peter Seibel"
```

## 原子操作
```
redis> WATCH mykey
redis> val = GET mykey
redis> val = val + 1
redis> MULTI
redis> SET mykey $val
redis> EXEC
```

# 补充
## ACID

| 特性             | 解析                                                                       | Redis 是否支持                                            |
|------------------|--------------------------------------------------------------------------|-----------------------------------------------------------|
| A<br>Atomicity   | 原子性<br>事务中的所有指令能全执行或全不执行<br>失败时可回滚至事务初始状态 | **NO**<br>事务因宕机等情况影响而执行失败，无重试或回滚操作 |
| C<br>Consistency | 一致性<br>事务前后，数据库的完整性不会被破坏                                | YES                                                       |
| I<br>Isolation   | 隔离性<br>允许多个事务交叉执行                                             | YES<br>单进程                                             |
| D<br>Durability  | 持久性<br>事务执行后，修改将永久保存<br>系统故障也不会丢失                  | **NO**<br>不存在持久化或异步实现                          |



# 参考
- [事务](https://redisbook.readthedocs.io/en/latest/feature/transaction.html)
- [ACID](https://zh.wikipedia.org/wiki/ACID)
- [redis的事务和watch](https://www.jianshu.com/p/361cb9cd13d5)
- [事务的实现](http://redisbook.com/preview/transaction/transaction_implement.html)