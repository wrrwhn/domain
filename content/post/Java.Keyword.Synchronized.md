---
title: "Java.Keyword.Synchronized"
date: "2019-01-01"
categories:
 - "整理"
tags:
 - "Java"
 - "Thread"
 - "Keyword"
toc: true
---

# 文档
## synchronized

- 原文
	
	```
	The Java Virtual Machine supports synchronization of both methods and sequences of instructions within a method by a single synchronization construct: the monitor.

	Method-level synchronization is performed implicitly, as part of method invocation and return (§2.11.8). A synchronized method is distinguished in the run-time constant pool's method_info structure (§4.6) by the ACC_SYNCHRONIZED flag, which is checked by the method invocation instructions. When invoking a method for which ACC_SYNCHRONIZED is set, the executing thread enters a monitor, invokes the method itself, and exits the monitor whether the method invocation completes normally or abruptly. During the time the executing thread owns the monitor, no other thread may enter it. If an exception is thrown during invocation of the synchronized method and the synchronized method does not handle the exception, the monitor for the method is automatically exited before the exception is rethrown out of the synchronized method.
	Synchronization of sequences of instructions is typically used to encode the synchronized block of the Java programming language. The Java Virtual Machine supplies the monitorenter and monitorexit instructions to support such language constructs. Proper implementation of synchronized blocks requires cooperation from a compiler targeting the Java Virtual Machine (§3.14).

	Structured locking is the situation when, during a method invocation, every exit on a given monitor matches a preceding entry on that monitor. Since there is no assurance that all code submitted to the Java Virtual Machine will perform structured locking, implementations of the Java Virtual Machine are permitted but not required to enforce both of the following two rules guaranteeing structured locking. Let T be a thread and M be a monitor. Then:

	The number of monitor entries performed by T on M during a method invocation must equal the number of monitor exits performed by T on M during the method invocation whether the method invocation completes normally or abruptly.

	At no point during a method invocation may the number of monitor exits performed by T on M since the method invocation exceed the number of monitor entries performed by T on M since the method invocation.

	Note that the monitor entry and exit automatically performed by the Java Virtual Machine when invoking a synchronized method are considered to occur during the calling method's invocation.
	```

- 理解
	- `synchronized` 结构通过 `monitor` 支持方法和块的同步的实现
	- 方法级同步
		- 于调用和返回时隐性支持
		- 存储于运行时静态池的 `method_info` 时，会添加以 `ACC_SYNCHRONIZED` 标志
			- 方法调用时，检测到有 `ACC_SYNCHRONIZED` 标志时，将让执行线程进入 `monitor`，并于调用结束或异常抛出前退出 `monitor`
			- 而当该线程拥有此 `monitor`时，其它线程将无法进入
	- 块级同步
		- JVM 通过 `monitorenter` 和 `monitorexit` 来支持块的同步
			- 两个指令需要数量匹配


# 示例


## 同步方法

- `SynchronizedTest.java`

	```java
    public synchronized void print() {
        System.out.println("synchronized method");
    }
	```

- `javap -verbose SynchronizedTest.class`

	```java
	public synchronized void print();
	  descriptor: ()V
	  flags: ACC_PUBLIC, ACC_SYNCHRONIZED
	  Code:
	    stack=2, locals=1, args_size=1
	       0: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
	       3: ldc           #3                  // String synchronized method
	       5: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
	       8: return
	    LineNumberTable:
	      line 12: 0
	      line 13: 8
	```

## 同步块

- `SynchronizedTest.java`

	```java
	    public void say() {
	        synchronized (this) {
	            System.out.println("synchronized block");
	        }
	    }
    ```

- `javap -verbose SynchronizedTest.class`

	```java
	public void say();
	  descriptor: ()V
	  flags: ACC_PUBLIC
	  Code:
	    stack=2, locals=3, args_size=1
	       0: aload_0
	       1: dup
	       2: astore_1
	       3: monitorenter
	       4: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
	       7: ldc           #5                  // String synchronized block
	       9: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
	      12: aload_1
	      13: monitorexit
	      14: goto          22
	      17: astore_2
	      18: aload_1
	      19: monitorexit
	      20: aload_2
	      21: athrow
	      22: return
	```


# 补充
## JVM.Object

| 存储位置 |       内容      |                        明细                        |
|----------|-----------------|----------------------------------------------------|
| Heap     |                 |                                                    |
|          | Object.Instance |                                                    |
|          |                 | HotSpot.Header<br>头信息                           |
|          |                 | Instance.Data<br>实例数据<br>相同宽度的分配在一起  |
|          |                 | 对齐填充数据<br>JVM 要求对象大小必须是 8B 的整数倍 |



## HotSpot.Header

### 通用版本

|            组成           |        字段       |           描述           |
|---------------------------|-------------------|--------------------------|
| 标记字段<br>Mark word     |                   | size= JVM.bits(32/64)        |
|                           | *Stack.Lock       | 指向锁记录的指针         |
|                           | Stack.Lock.statuc | 锁状态标志               |
|                           | *Object.Monitor   | 指向重量级锁的指针       |
|                           | HashCode          | 哈希码值                 |
|                           | Age               | GC 分代年龄              |
|                           | Thread.ID         | 偏向线程 ID              |
|                           | epoch             |                          |
| 类型指针<br>Klass Pointer |                   | 可通过句柄访问或直接访问 |

### 官方版本

- ![JVM_Object_Header.png](http://doc.yqjdcyy.com/2bc2d37b-60f0-4705-a5f9-28e3a66e9d73.png)

### 锁版本

- 此处以「官方版本」的 32bits 为例

	- ![JVM_Object_Header_Lock.png](http://doc.yqjdcyy.com/7020b9a1-7894-4dac-9581-dab260686770.png)


## Header.Lock
### 锁状态

|   名称   | 锁标志 | 偏向锁标志 |                                    描述                                   |
|----------|--------|------------|---------------------------------------------------------------------------|
| 无锁状态 |     01 |          0 |                                                                           |
| 轻量级锁 |     00 |            |                                                                           |
| 重量级锁 |     10 |            | 多个线程竞争锁<br>线程通过自旋以循环获取锁                                |
| GC 标记  |     11 |            |                                                                           |
| 偏向锁   |     01 |          1 | 单进程同步时使用<br>减少不必要的轻量级锁依赖<br>相对节省多次 CAS 原子操作 |


### 升级
- 单向
- 次序
	- 无锁
	- 偏向锁
	- 轻量锁
	- 重量锁


### 流程
- 「轻量级锁-重量级锁」升级过程
	- Record.Lock
		- 代码开始同步时，若同步对象为无锁状态，则 VM 将于当前线程建立锁记录 `Lock Record`
		- 锁记录中存储对象的 `Mark word` 的拷贝，又名 `Displaced Mark Word`
	- COPY
		- 将对象实现状况的 `Mark Word` 拷贝至栈帧的锁记录中
	- CAS
		- VM 尝试使用 CAS 将对象实例的 `Mark Word` 更新指向锁记录，并将锁记录的 `owner` 指针指向对象实例的 `Mark Word`
	- CAS.Success
		- 将锁状态更新为轻量级锁
	- CAs.Fail
		- 首先检查是否对象实例的 `Mark Word` 是否已经指向栈帧
			- 如果是，则说明已经拥有该对象的锁，开始同步操作
		- 确认为多线程竞争，膨胀为重量级锁，并将 `Mark Word` 指向 `Object.Monitor`


- 「无锁-偏向锁-轻量级锁」升级过程
	- Biased_Lock
		- 前置条件
		- 判断 `Mark Word` 中的偏向锁标志是否开启
	- Biased.Lock== 1 and Thread.ID== this.iD
		- 若可偏向，且偏向进程 ID 为当前进程 ID，则竞选结束，继续执行同步代码
	- CAS
		- 通过 CAS 操作竞争锁
	- CAS.Success
		- 将 `Mark Word` 中的进程 ID 更新为当前进程 ID
		- 继续执行同步代码
	- CAS.Fail
		- 判断有多个进程在竞争
		- 升级为轻量级锁
			- 时机
				- 为全局安全点
					- SafePoint
					- 无字节码正在执行
			- 操作
				- 挂起获得偏向锁的进程
				- 偏向锁升级为轻量级锁
				- 阻塞线程继续执行同步代码

### 图示

- 偏向锁版本
	- ![Java.Synchronized.Biased.png](http://doc.yqjdcyy.com/95b86b47-6c70-4dac-86fb-015d7bdcaf97.png)

- 完整版本
	- ![java-synchronized.jpg](http://doc.yqjdcyy.com/eb603ae6-15d7-416a-86a3-62fba06acac8.jpg)


## 自旋锁
- 描述
	- 互斥情况下，线程 A 获得锁后，线程 B 申请，则 B 阻塞等待
	- 而如若线程上下文的切换成本，较之锁竞争的成本更高，则可让 B 不放弃 CPU 原地等待持有者 A 释放锁

- 问题
	- 占据 CPU 时长过度
	- 死锁
	- ABA 异常

- 场景
	```java
	public class SpinLock {

	  private AtomicReference<Thread> sign =new AtomicReference<>();

	  public void lock(){
	    Thread current = Thread.currentThread();
	    while(!sign .compareAndSet(null, current)){
	    }
	  }

	  public void unlock (){
	    Thread current = Thread.currentThread();
	    sign .compareAndSet(current, null);
	  }
	}
	```

# 参考
- [Synchronized的实现原理](http://www.hollischuang.com/archives/1883)
- [2.11.10. Synchronization](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html#jvms-2.11.10)
- [从jvm源码看synchronized](https://www.cnblogs.com/kundeg/p/8422557.html)
- [Java对象的创建、内存布局、访问定位](https://blog.csdn.net/u011080472/article/details/51321769)
	- 详细描述对象访问的定位方式
- [What is in java object header](https://stackoverflow.com/questions/26357186/what-is-in-java-object-header)
- [不得不了解的对象头](https://blog.csdn.net/zhoufanyang_china/article/details/54601311)
	- 详细描述了锁状态的转换
- [SynchronizedTest.txt](http://doc.yqjdcyy.com/d236db88-5e9e-4db1-8fc9-41194b742be3.txt)
	- 反编译的内容
- [java自旋锁](https://www.jianshu.com/p/dfbe0ebfec95)