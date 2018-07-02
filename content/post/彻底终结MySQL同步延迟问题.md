---
title: "彻底终结MySQL同步延迟问题"
date: "2018-02-07"
categories:
 - "阅读"
tags:
 - "MYSQL"
toc: true
---


# 彻底终结MySQL同步延迟问题

## 参考
- [彻底终结MySQL同步延迟问题](https://www.jianshu.com/p/ed19bb0e748a)
- [Binary Logging Options and Variables](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html)
- [Estimating potential for MySQL 5.7 parallel replication](https://www.percona.com/blog/2016/02/10/estimating-potential-for-mysql-5-7-parallel-replication/)


## 版本
- MySQL.Version
	- 5.7
- Storage.Engine
	- TokuDB

## 影响因素
### 网络
- 主机或者从机的带宽打满
- 主从之间网络延迟很大

### 机器性能
- 从机使用了烂机器
	- 比如主机使用SSD而从机还是使用的SATA

- 从机高负载
	- 很多业务会在从机上做统计，把从机服务器搞成高负载
	- 使用 `top` 查看使用情况

- 从机磁盘有问题
	- 检查磁盘、raid卡、调度策略
		- raid卡电池充放电的时候，在没有设置强行write back的情况下得会将write back模式修改为write through。
	- 使用 `iostat` 命令查看 DB 数据盘的 IO 情况，是否是单个 IO 的执行时间很长，块大小和磁盘队列情况等，


### 大事务
- 检查是否经常有大事务
	- 在RBR模式下，执行带有大量的delete操作
	- 在MBR模式下删除的时候添加了不确定语句（类似limit），
	- 一个表的alter操作等
- 通过查看 `processlist` 相关信息
- 使用 `mysqlbinlog` 查看 binlog 中的 SQL 就能快速进行确认


### 锁
- 检查锁冲突
	- 从机上有一些 `select  ....  for update`的 SQL，
	- 使用了MyISAM引擎
- 去 `processlist` 
- 查看 `information_schema` 下面和锁以及事务相关的表


### 参数
- innodb
	- 使用环境调整 `innodb_flush_log_at_trx_commit`、`sync_binlog` 参数来提升复制速度，
- TokuDB
	- 优化`tokudb_commit_sync`、`tokudb_fsync_log_perid`、`sync_binlog` 等参数来做调整


### 多线程
- 版本
	- 5.1 & 5.5
		- mysql 单线程复制瓶颈
	- 5.6
		- mysql 正式支持多线程复制

- 参数
	- 通过 `show processlist` 查看是否有多个同步线程，也可以查看参数的方式查看是否使用多线程
		- show variables like '%slave_parallel%'

		|     Variable_name      |  Value   |
		|------------------------|----------|
		| slave_parallel_type    | DATABASE |
		| slave_parallel_workers | 0        |

		|     Variable_name      |     Value     |
		|------------------------|---------------|
		| slave_parallel_type    | LOGICAL_CLOCK |
		| slave_parallel_workers | 8             |

	- 启用多线程	
		- `STOP SLAVE SQL_THREAD;SET GLOBAL slave_parallel_type='LOGICAL_CLOCK';SET GLOBAL slave_parallel_workers=8;START SLAVE SQL_THREAD;`

- 统计
	- 将线上从机相关统计打开（出于性能考虑默认是关闭的）
	> 
	UPDATE performance_schema.setup_consumers SET ENABLED = 'YES' WHERE NAME LIKE 'events_transactions%';
	UPDATE performance_schema.setup_instruments SET ENABLED = 'YES', TIMED = 'YES'WHERE NAME = 'transaction';

	- 创建一个查看各个同步线程使用量的视图
	> 
	USE test;
	CREATE VIEW rep_thread_count AS SELECT a.THREAD_ID AS THREAD_ID,a.COUNT_STAR AS COUNT_STAR FROM performance_schema.events_transactions_summary_by_thread_by_event_name a WHERE a.THREAD_ID in (SELECT b.THREAD_ID FROM performance_schema.replication_applier_status_by_worker b);


	- 统计各个同步线程的使用比率
	> 
	SELECT SUM(COUNT_STAR) FROMrep_thread_count INTO @total;
	SELECT 100*(COUNT_STAR/@total) AS thread_usage FROMrep_thread_count;


### 组提交

- LOGICAL_CLOCK的方式下，并发执行的多个事务只要能在同一时刻commit，就说明线程之间没有锁冲突
- 尽可能地使所有线程能在同一时刻提交，这样就能很大程度上提升从机的执行的并行度，从而减少从机的延迟。

- 参数
	- `binlog_group_commit_sync_delay`
		- 延迟SQL的响应
	- `binlog_group_commit_sync_no_delay_count`
		- 在达到binlog_group_commit_sync_no_delay_count设定的值的时候，不管是否达到了binlog_group_commit_sync_delay设置定的阀值，都立即进行提交