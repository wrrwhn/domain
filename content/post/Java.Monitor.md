---
title: "Java.JVM.Monitor"
date: "2018-12-30"
categories:
 - "整理"
tags:
 - "Java"
 - "JVM"
 - "Thread"
toc: true
---


# 文档
## monitor
- 描述
	- 原文

		```java
		Synchronizationis built around an internal entity known as the intrinsic lock or monitor lock. (The API specification often refers to this entity simplyas a “monitor.”)，
		Every object has an intrinsic lock associated with it.
		By convention, a thread that needs exclusive and consistent access to an object’s fields has to acquire the object’s intrinsic lock before accessing them, 
		and then release the intrinsic lock when it’s done with them.
		```
	- 理解
		- 每个对象都拥有一内部事务锁
		- 当进程要访问字段前，需先排他性地获得该内部锁，并于操作完成后释放该锁


## monitorenter

- 原文
	
	```
	Each object is associated with a monitor. A monitor is locked if and only if it has an owner. The thread that executes monitorenter attempts to gain ownership of the monitor associated with objectref, as follows:
		If the entry count of the monitor associated with objectref is zero, the thread enters the monitor and sets its entry count to one. The thread is then the owner of the monitor.
		If the thread already owns the monitor associated with objectref, it reenters the monitor, incrementing its entry count.
		If another thread already owns the monitor associated with objectref, the thread blocks until the monitor's entry count is zero, then tries again to gain ownership.

	A monitorenter instruction may be used with one or more monitorexit instructions (§monitorexit) to implement a synchronized statement in the Java programming language (§3.14). The monitorenter and monitorexit instructions are not used in the implementation of synchronized methods, although they can be used to provide equivalent locking semantics. Monitor entry on invocation of a synchronized method, and monitor exit on its return, are handled implicitly by the Java Virtual Machine's method invocation and return instructions, as if monitorenter and monitorexit were used.

	The association of a monitor with an object may be managed in various ways that are beyond the scope of this specification. For instance, the monitor may be allocated and deallocated at the same time as the object. Alternatively, it may be dynamically allocated at the time when a thread attempts to gain exclusive access to the object and freed at some later time when no thread remains in the monitor for the object.

	The synchronization constructs of the Java programming language require support for operations on monitors besides entry and exit. These include waiting on a monitor (Object.wait) and notifying other threads waiting on a monitor (Object.notifyAll and Object.notify). These operations are supported in the standard package java.lang supplied with the Java Virtual Machine. No explicit support for these operations appears in the instruction set of the Java Virtual Machine.
	```

- 理解
	- 每个对象都关联一个 `monitor`，当其拥有所有者时， `monitor` 将被锁定
		- 当对象引用的 `monitor` 的持有者为空（进入的数量为0）时，线程才能拥有该 `monitor` 并将其进入数更新为 `1`
		- 当拥有 `monitor` 的持有者重复进入，则将进入数自增
		- 当 `monitor` 有拥有者时，其它进程必须阻塞以等待拥有者退出，进入数重置为 `0`
	- 对象与 `monitor` 同时分配、释放
	- `java.lang.Object` 对象通过 `wait` 和 `notifyAll` 隐性支持了 monitor 的 进入和退出

## monitorexit

- 原文

	```
	The objectref must be of type reference.

	The thread that executes monitorexit must be the owner of the monitor associated with the instance referenced by objectref.

	The thread decrements the entry count of the monitor associated with objectref. If as a result the value of the entry count is zero, the thread exits the monitor and is no longer its owner. Other threads that are blocking to enter the monitor are allowed to attempt to do so.
	One or more monitorexit instructions may be used with a monitorenter instruction (§monitorenter) to implement a synchronized statement in the Java programming language (§3.14). The monitorenter and monitorexit instructions are not used in the implementation of synchronized methods, although they can be used to provide equivalent locking semantics.

	The Java Virtual Machine supports exceptions thrown within synchronized methods and synchronized statements differently:

	Monitor exit on normal synchronized method completion is handled by the Java Virtual Machine's return instructions. Monitor exit on abrupt synchronized method completion is handled implicitly by the Java Virtual Machine's athrow instruction.

	When an exception is thrown from within a synchronized statement, exit from the monitor entered prior to the execution of the synchronized statement is achieved using the Java Virtual Machine's exception handling mechanism (§3.14).
	```

- 理解
	- 调用 `monitorexit` 的线程仅可为拥有其 `monitor` 的对象引用所关联的实例
	- 线程调用 `monitorexit` 时，将减少其关联的 `monitor` 的进入数，并当进入数减少为 0 时，失去对该对象的所有权
	- 异常处理机制
		- 同步方法将由 JVM 的 `athrow` 指令隐性处理
		- 同步块则由 JVM 的异常处理机制接收



# 实现
## ObjectMonitor

### 结构

- HotSpot.ObjectMonitor

|     字段    |              作用              |
|-------------|--------------------------------|
| _owner      | 持有线程                       |
| _recursions | 持有线程的进入次数             |
| _cxq        | 多线程竞争锁时使用<br>单向链表 |
| _EntryList  | 待竞选线程池<br>双向循环链表               |
| _WaitSet    | 等待线程池<br>双向循环链表               |

### 流程

- ![Java.Object.Monitor.png](http://doc.yqjdcyy.com/eddc8cc2-61d7-4b60-bd8e-d2de51e424e4.png)



# 参考
- [monitorenter](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.monitorenter)
- [monitorexit](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.monitorexit)
- [markOop.hpp](http://hg.openjdk.java.net/jdk8/jdk8/hotspot/file/87ee5ee27509/src/share/vm/oops/markOop.hpp)
	- object-monitor 源码
- [从jvm源码看synchronized](https://www.cnblogs.com/kundeg/p/8422557.html)
- [Java的wait()、notify()学习三部曲之一：JVM源码分析](https://blog.csdn.net/boling_cavalry/article/details/77793224)
	- 事例+源码