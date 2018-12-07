---
title: "Redis.复制"
date: "2018-12-06"
categories:
 - "整理"
tags:
 - "Redis"
toc: true
---

# 描述
- `master` 服务器将内容同步至 `slave` 服务器
    - `slave` 服务器在与 `master` 断开后，将尝试重连以更新获取精确副本

# 机制
- `异步`复制
    - 高延迟、高性能

- `同步`加载
    - `slave` 可在后台异常下载，待完成后再`阻塞`切换至主进程进行内容更新
        - 删除原有数据
            - v4.0+ 可于其它线程中操作
        - 加载新的数据
    - 异步拉取数据时，使用旧的数据处理查询请求

- `slave` 服务器可复制其它 `slave` 服务
    - 从 `master` 接收一致的复制流

- 服务器为数据集生成以相应 `runid` 和相应的偏移量 `offset`
    - 默认配置
        - 缓冲区
            - `backlog`
            - 默认大小为 **1M**
    - 同步情况
        - 部分重同步
            - 仅 `offset` 不一样，且偏移量小于缓冲区大小
        - 全量重同步
            - `runid`  不一致
            - `offset` 偏移量超出缓冲区大小
    - `runid` 规则
        - 生成
            - 主库进程启动后生成的唯一标识
            - 进程 ID + 随机数
        - 同步
            - 首次同步时，将把 `runid` 和 `offset` 一并返回给 `slave`
    - 指令
        - `PSYNC`

# 流程
## 全量同步
- `master` 开启后台进程
    - 产生 RDB 文件
    - 缓存客户端的写入命令
- 将 RDB 文件传输给 `slave`，保存到磁盘后，再行加载至内存
- 后续再将所有缓冲命令发送给 `slave`

# 拓扑

| Level-1 | Level-2 |  Level-2  |
|---------|---------|-----------|
| Master  | Slave-1 |           |
|         | Slave-2 | Slave-2-1 |
|         |         | Slave-2-2 |
|         |         | Slave-2-3 |

# 补充
- **故障转移**期间，**写入**操作存在**丢失**可能
- `master` 服务器**关闭持久化**时，应**避免自动重启**
    - 重启时由于没有持久化数据，导致节点数据集为空
    - 复制至其它节点时，导致其它节点的数据画本也会被清空
- redis 4.0 引入了 `replid2` 以存放主库的 `replid`
    - 避免原有场景下所有从库的全量更新
        - 于从库切换为主库时使用
    - 支持服务重启后的增量同步
        - rdb 备份时，会一并持久化 `replid` 和 `offset`

# 参考
- [复制](http://www.redis.cn/topics/replication.html)
- [PSYNC](http://redisdoc.com/server/psync.html)
- [Redis-4.0 psync 优化](http://www.hulkdev.com/posts/redis_new_psync)