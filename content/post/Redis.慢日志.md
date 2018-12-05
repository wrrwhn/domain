---
title: "Redis.慢日志"
date: "2018-12-04"
categories:
 - "整理"
tags:
 - "Redis"
toc: true
---

# 描述

- 用于记录`执行时间超出限制`的、`有限`的命令日志
    - 执行时间单为该命令的执行时间，不包括网络响应、IO延迟

# 结构

| 结构         | 字段                    | 类型          | 描述                                 |
|--------------|-------------------------|---------------|--------------------------------------|
| redisServer  |                         |               |                                      |
|              | slowlog                 | list *        | 慢日志存储链表                       |
|              | slowlog_entry_id        | long long     | 当前记录的慢日志的 ID                |
|              | slowlog_log_slower_than | long long     | 慢日志的时限值                       |
|              | slowlog_max_len         | unsigned long | 慢日志的记录数上限                   |
| slowlogEntry |                         |               |                                      |
|              | argv                    | robj**        | 执行命令，以空格等间隔开              |
|              | argc                    | int           | 命令参数的数量                       |
|              | long id                 | long          | 唯一标识 ID                          |
|              | long duration           | long          | 命令执行所耗时长<br>单位为纳纱       |
|              | time                    | time_t        | 命令执行的时间<br>单位为 Unix 时间戳 |


# 命令

|      命令     |           作用           |                                         示例                                        |
|---------------|--------------------------|-------------------------------------------------------------------------------------|
| slowlog get   | 获取慢日志记录           | slowlog get: 获取所有的慢日志记录<br>slowlog get <number>: 获取指定数量的慢日志记录 |
| slowlog len   | 获取慢日志记录的存储数量 |                                                                                     |
| slowlog reset | 清空慢日志记录           |                                                                                     |

# 示例

```c
127.0.0.1:37652> slowlog get 1
1) 1) (integer) 448             // ID
   2) (integer) 1543825754      // 命令执行的时间点
   3) (integer) 17381           // 命令执行的消耗时长
   4) 1) "SSCAN"                // 执行命令的数组形式
      2) "operation:user:list"
      3) "0"
      4) "COUNT"
      5) "10000"

127.0.0.1:37652> slowlog len
(integer) 128
      
127.0.0.1:37652> slowlog reset
OK

127.0.0.1:37652> config get slowlog-log-slower-than 
1) "slowlog-log-slower-than"
2) "10000"

127.0.0.1:37652> config get slowlog-max-len
1) "slowlog-max-len"
2) "128"
```

# 参考
- [慢查询日志](https://redisbook.readthedocs.io/en/latest/feature/slowlog.html)
- [查看并 redis慢日志](https://blog.csdn.net/xiaolyuh123/article/details/79209285)
- [SLOWLOG](http://github.tiankonguse.com/doc/redis/server/slowlog.html)