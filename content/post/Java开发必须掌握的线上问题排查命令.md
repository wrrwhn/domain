+++
date = "2018-06-05T16:30:00+08:00"
title = "Java开发必须掌握的线上问题排查命令"
draft = false
tags = ["阅读","Java"]
share = true
+++

[TOC]

# [Java开发必须掌握的线上问题排查命令](http://www.hollischuang.com/archives/1561)

## 常用命令
### JPS
- 功能
	- 显示进程的 PID
- 示例
	- `jps`
	- `jps -v pid`
		- 显示指定 PID 对应程序的`虚拟机参数`
	- `jps -m pid`
		- 显示程序`启动参数`
	- `jps -l pid`
		- 显示`主类的绝对路径`

### JSTAT
- 功能
	- 显示进程中类装载/ 内存/ 垃圾回收及 JIT 编译等数据
- 示例
	- `jstat -gc [PID] [milliseconds] [times]`
		- 查询指定[PID]进程的`垃圾回收`情况, 每[milliseconds]毫秒一次,共[times]次
	- `jstat -gccause`
		- 显示上次 GC 的`原因`
	- `jstat -calss`
		- 显示`类`装载、类卸载、总空间以及所消耗的时间
	
### JMAP
- 功能
	- 生成堆栈快照
- 示例
	- `jmap -heap [PID]`
		- 查看 java `堆`的使用情况
	- `jmap -histo [PID]`
		- 查看`堆内存`中的对象数量及大小
	- `jmap -histo:live [PID]`
		- JVM会`先触发 gc`，然后再`统计`信息
	- `jmap -dump:format=b,file=[file] [PID]`
		- 将内存使用的详细情况输出到文件 [file] 中

### JHAT
- 功能
	- 配合 `JMAP` 使用,分析堆栈文件
- 示例
	- `jhat heapDump`
		- 解析Java堆转储文件,并启动一个 web server

### JSTACK
- 功能
	- 生成当前的线程快照
- 示例
	- `jstack [PID]
		- 查看线程情况
	- `jstack -F [PID]
		- `正常输出不被响应时`，使用该指令
	- `jstack -l [PID]
		- 除堆栈外，显示关于`锁`的附件信息


## 问题定位
### 频繁 GC 或内存溢出
- 使用 `jps` 查看线程ID
- 使用 `jstat -gc [PID] [MS] [TIME]` 查看gc情况，一般比较关注 **PERM区** 的情况，查看 `GC`的 **增长情况**
- 使用 `jstat -gccause` 额外输出**上次GC原因**
- 使用 `jmap -dump:format=b,file=heapDump [PID]` 以*生成堆转储文件**
- 使用 `jhat` 或者可视化工具（Eclipse Memory Analyzer 、IBM HeapAnalyzer）分析堆情况
- 结合代码解决内存溢出或泄露问题

### 死锁
- 使用`jps`查看线程ID
- 使用`jstack [PID]` 以查看线程情况

