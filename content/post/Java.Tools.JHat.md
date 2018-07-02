---
title: "Java.Tools.JHat"
date: "2018-06-07"
categories:
 - "整理"
tags:
 - "Java"
 - "tools"
toc: true
---


# JHat
## 作用
- 打印出进程内存中，所有对象的情况

## 调用
- `jhat [ options ] heap-dump-file`

## 示例
- `jmap -dump:format=b,file=d:/block-jmap.dump 15240`
- `jhat d:/block-jmap.dump`
	```
	Reading from d:/block-jmap.dump...
	Dump file created Wed Jun 06 08:34:24 CST 2018
	Snapshot read, resolving...
	Resolving 851039 objects...
	Chasing references, expect 170 dots.........
	Eliminating duplicate references............
	Snapshot resolved.
	Started HTTP server on port 7000
	Server is ready.
	```
	- [Dump查看界面](http://localhost:7000)
	- [OQL执行界面](http://localhost:7000/oql/)


# Reference
- 官方
	- [jmap](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jhat.html)   
- 整理
	- [Java命令学习系列（五）——jhat](http://www.hollischuang.com/archives/10470)
- OQL
	- [OQL帮助信息](http://localhost:7000/oqlhelp/)
	- [虚拟机学习系列 - 附 - OQL（对象查询语言）](http://su1216.iteye.com/blog/1535776)

