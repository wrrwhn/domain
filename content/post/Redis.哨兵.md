---
title: "Redis.哨兵"
date: "2018-12-06"
categories:
 - "整理"
tags:
 - "Redis"
toc: true
---


# 描述
- Redis 2.8 后版本支持
- Sentinel 管理多个 Redis 实例，并实现对其的监控、通知和**故障转移**

# 架构

- 描述

|  分类 |         实例         |
|-------|----------------------|
| 监控  |                      |
|       | Sentinel* n<br>n>= 3 |
| Redis |                      |
|       | Master               |
|       | Slave* n<br>n>=2     |

- 图示
	- ![REDIS-SENTINEL.png](http://doc.yqjdcyy.com/ad7f2a0a-8b00-4d2d-8d4a-53415d051854.png)

# 命令

|   发送   |   接收   |               命令              |                                      作用                                     |        频率        |
|----------|----------|---------------------------------|-------------------------------------------------------------------------------|--------------------|
| Sentinel | Redis    |                                 |                                                                               |                    |
|          |          | PING                            | 检查节点状态                                                                  | PING/ s             |
|          |          | INFO                            | 获取从节点信息                                                                | 主节点主观下线时 INFO/ s<br>INFO/ 10s |
|          |          | PUBLISH                         | 监控节点的 `__sentinel__:hello` 的`channel`<br>推送自身信息<br>推送主节点配置 |                    |
|          |          | SUBSCRIBE                       | 通过 `__sentinel__:hello` 获取监控服务的其它 `Sentinel` 节点                  |                    |
| Sentinel | Sentinel |                                 |                                                                               |                    |
|          |          | PING                            | 检查节点状态                                                                  | PING/ s             |
|          |          | SENTINEL:is-master-down-by-addr | 协商主节点状态<br>如果主节点客观下线，则投票选出新的主节点                    |                    |


# 示例

## 配置

- Redis-Sentinel.conf

	```c
	port 26379

	# 工作目录
	dir ./

	# sentinel monitor <master-name> <ip> <redis-port> <quorum>
	# quorum 为要求多少个 Sentinel 认定，才可判断 master 客观下线
	sentinel monitor mymaster 127.0.0.1 6379 2

	sentinel auth-pass mymaster 123456  

	# 心跳超期间隔，默认为 30s；超出则判定为主观下线
	sentinel down-after-milliseconds mymaster 30000  

	# 故障转移时，限定可向 master 同步的 slave 数量
	sentinel parallel-syncs mymaster 1  

	# 故障转移超时时间，默认为 180s	
	sentinel failover-timeout mymaster 180000

	# 故障时执行脚本，执行上限 60s，重试次数 10次
	sentinel notification-script mymaster /var/redis/notify.sh

	sentinel client-reconfig-script mymaster /var/redis/reconfig.sh
	```

- Redis-Client.conf

	```c
	daemonize yes
	pidfile /var/run/redis-6379.pid
	logfile /var/log/redis/redis-6379.log
	port 6379
	bind 0.0.0.0
	timeout 300
	databases 16
	dbfilename dump-6379.db
	dir ./redis-workdir
	masterauth 123456
	requirepass 123456

	// 从节点配置
	slaveof 127.0.0.1 16379
	```

## 启动
- `redis-server /usr/local/redis-sentinel/redis-16379.conf`
- `redis-sentinel sentinel-16380.conf`

## 关键日志

## 故障次数
	
```c
+new-epoch 1
```

## 16379 节点主观下线

```c
+sdown master master 127.0.0.1 16379
```

## 16379 节点被判定客观下线
	
```c
+odown master master 127.0.0.1 16379 #quorum 3/2
```
## 将主节点切换为 26379
	
```c
+switch-master master 127.0.0.1 16379 127.0.0.1 26379

+slave slave 127.0.0.1:36379 127.0.0.1 36379 @ master 127.0.0.1 26379
+slave slave 127.0.0.1:16379 127.0.0.1 16379 @ master 127.0.0.1 26379
```

# 流程

- 实例距最后一次 PING 的时间，超过 `down-after-milliseconds: 30s`，则被认定为**主观下线**
- 指定数量 `quorum: 2` 在指定时间内通过该节点下线的判断，则认定为 **客观下线**
- `Sentinel` 间协商投票出新的主节点，并通过 `INFO` 将剩余从节点指向新的主节点，进行数据复制

# 补充

## 高可用方案

|  方案  |                 优点                 |                          缺点                          |
|--------|--------------------------------------|--------------------------------------------------------|
| 持久化 | 数据备份                             |                                                        |
| 复制   | 实现读的负载<br>故障恢复能力         | 无法自动恢复<br>写操作无法负载<br>存储无法摆脱单机限制 |
| 哨兵   | 复制的基础上，实现故障恢复的自动实现 | 写操作无法负载<br>存储无法摆脱单机限制                 |
| 集群   | 实现写的负载<br>摆脱存储的单机限制   |                                                        |


# 参考
- [Redis哨兵模式与高可用集群](https://juejin.im/post/5b7d226a6fb9a01a1e01ff64)
	- 流程图、示例等相当完整
- [sentinel.conf](http://download.redis.io/redis-stable/sentinel.conf)
	- 完整配置
