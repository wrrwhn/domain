---
title: "Java.JVM.内存结构"
date: "2018-12-26"
categories:
 - "整理"
tags:
 - "Java"
 - "JVM"
toc: true
---


# 作用
- 通过了解 JVM 内存情况，便于快速定位、解决服务器性能问题
- 便于串联、理解 Java 运行的整体流程

# 结构

## JDK 1.6/1.7
- 精简版
	- ![JMM-v1.png](http://doc.yqjdcyy.com/efc12233-95aa-4956-a369-975ebe6872c5.png)
- 细划版
	- ![JMM-v2.png](http://doc.yqjdcyy.com/479b5392-a40b-4846-8393-715224f42ee5.png)
- 精确版
	- ![JVM-v3.png](http://doc.yqjdcyy.com/d7bedf9a-1e26-43f2-9f23-bce6a4b83b77.png)

## JDK 1.8
- ![JVM-1.8.png](http://doc.yqjdcyy.com/0ad7c720-362e-4e9b-9172-93e5b97e491f.png)


# 作用

| 分区        | 分块                 | 底层                                 | 细节                                                                                                                                                                        |
|-------------|----------------------|--------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Heap        |                      |                                      | 随 VM 启动时创建，不需连续<br>**所有线程共享**<br>用于创建**对象实例**、分配**数组**                                                                                          |
|             | Young Generation     |                                      |                                                                                                                                                                             |
|             |                      | Eden Generation                      | 新创建的对象                                                                                                                                                                |
|             |                      | From<br>Survivor 0                   | 上一轮 Minor GC 存活下来的对象                                                                                                                                              |
|             |                      | To<br>Survivor 1                     | 无数据空间，用于下一轮 Young GC 时存放未被回收的数据                                                                                                                         |
|             |                      | Virtual                              |                                                                                                                                                                             |
|             | Old Generation       |                                      |                                                                                                                                                                             |
|             |                      | Tenured                              | 存放经过一定生命周期仍然存在的对象<br>大对象（新生代分配不了）                                                                                                                |
|             |                      | Virtual                              |                                                                                                                                                                             |
| Method Area |                      |                                      | 随 VM 启动时创建，大小可根据需要进行扩容、收缩，不需连续                                                                                                                       |
|             | Permanent Generation |                                      |                                                                                                                                                                             |
|             |                      | Runtime Constant Pool                | 常量<br>编译时的数值、文字，运行时的方法与字段的引用                                                                                                                          |
|             |                      | Filed & Method Data                  | 类信息、静态变量                                                                                                                                                             |
|             |                      | Code                                 | 即时编译的代码                                                                                                                                                              |
|             |                      | Virtual                              |                                                                                                                                                                             |
| Native Area |                      |                                      |                                                                                                                                                                             |
|             | Code Cache           |                                      |                                                                                                                                                                             |
|             |                      | Thread.PC<br>Program Pointer Counter | 记录当前线程所**执行的字节码的行号**<br>字节码解释器通过更新该值以获取下一条要执行的指令；分支、循环、跳转、异常和线程恢复均依赖计数器<br>执行 Native 方法时，计数值为 Undefined |
|             |                      | Thread.Stack                         | 方法执行时创建、入栈，完成时出栈<br>**栈帧**由局部变量表、操作数栈、动态链接和方法出口、分发异常、运行时常量池入口组成                                                            |
|             |                      | Thread.Native Methos Stack           | 存储虚拟机使用到的 Native 方法服务<br>如若 JVM 实现不支持加载本地方法，或不依赖于传统栈的，则无需提醒                                                                         |
|             |                      | Compile                              |                                                                                                                                                                             |
|             |                      | Native                               |                                                                                                                                                                             |
|             |                      | Virtual                              |                                                                                                                                                                             |

# 异常

|        异常        |      可能发生区域      |
|--------------------|------------------------|
| StackOverflowError |                        |
|                    | Native Method Stacks   |
| OutOfMemoryError   |                        |
|                    | Heap                   |
|                    | Method Area            |
|                    | Run-Time Constant Pool |
|                    | Native Method Stacks   |


# 流程

|         阶段        |                                                                            动作                                                                            |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 创建对象            | 在 Edgn Space 创建用户                                                                                                                                     |
| Eden Space 空间已满 | Minor GC/ Young GC<br>将 Eden/ From Survivor 空间不能被回收的移动到 To Survivor<br>To Survivor 对象的 age ++<br>将 From/To 标识更新，保证 To Survivor 为空 |
| Old Space 空间已满  | Major GC/ Full GC<br>对整个 Heap 空间进行扫描、回收                                                                                                        |


# 默认值|调整

|     模块     |          默认值          |                  情况                 |        调整值         |
|--------------|--------------------------|---------------------------------------|-----------------------|
| Heap         |                          |                                       |                       |
|              | Memory/ 64               |                                       |                       |
|              |                          | Heap.free< `MinHeapFreeRatio: 40>`%   | Heap.size+ ？<= `Xmx` |
|              |                          | Heap.Used> `XX:MaxHeapFreeRatio: 70`% | Heap.size- ?>= `Xms`  |
| Permanent    |                          |                                       |                       |
|              | Memory/ 64               |                                       |                       |
| Code/ Native |                          |                                       |                       |
|              | 30m/client<br>48m/server |                                       |                       |


# 示例

## 触发 Minor GC

- Code
	```java
	final int M = 1024* 1024;

	byte[] b0=new byte[2* M];
	byte[] b1=new byte[2* M];
	byte[] b2=new byte[2* M];
	byte[] b3=new byte[4* M];
	```

- VM args

	| 参数                  | 作用                 |
	|-----------------------|----------------------|
	| `-XX:+UseSerialGC`    | 使用串行垃圾回收器   |
	| `-verbose:gc`         |                      |
	| `-XX:+PrintGCDetails` | 打印 GC 明细信息     |
	| `-Xms20M`             | 新生代最小内存为 20M |
	| `-Xmx20M`             | 新生代最大内存为20M  |
	| `-Xmn10M`             |                      |
	| `-XX:SurvivorRatio=8` | eden/ survivor= 8    |

- Log

	```log
	[GC (Allocation Failure) [DefNew: 6817K->848K(9216K), 0.0043662 secs] 6817K->4945K(19456K), 0.0044138 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
	Heap
	 def new generation   total 9216K, used 7201K [0x00000000fec00000, 0x00000000ff600000, 0x00000000ff600000)
	  eden space 8192K,  77% used [0x00000000fec00000, 0x00000000ff2343d0, 0x00000000ff400000)
	  from space 1024K,  82% used [0x00000000ff500000, 0x00000000ff5d43f0, 0x00000000ff600000)
	  to   space 1024K,   0% used [0x00000000ff400000, 0x00000000ff400000, 0x00000000ff500000)
	 tenured generation   total 10240K, used 4096K [0x00000000ff600000, 0x0000000100000000, 0x0000000100000000)
	   the space 10240K,  40% used [0x00000000ff600000, 0x00000000ffa00020, 0x00000000ffa00200, 0x0000000100000000)
	 Metaspace       used 3284K, capacity 4496K, committed 4864K, reserved 1056768K
	  class space    used 357K, capacity 388K, committed 512K, reserved 1048576K
	```

- 解析

	| 字段               | 解析                                                 |
	|--------------------|----------------------------------------------------|
	| GC                 | GC类型                                               |
	| Allocation Failure | 触发GC的原因                                         |
	| DefNew             | 触发的垃圾回收器<br>此值表示使用了**串行回收收集器** |
	| 6817K->848K        | **年轻代**内存使用值的变化情况                       |
	| (9216K)            | 年轻代内存大小                                       |
	| 6817K->4945K       | **堆**内存使用的情况变化                             |
	| (19456K)           | 堆内存大小                                           |
	| Times.user         | 垃圾回收**线程**消耗的 CPU 时间                      |
	| Times.sys          | **系统**调用和等待事件的时间                         |
	| Times.real         | 应用程序暂停的时间                                   |

# 参数

## 格式

	|           格式           |                      含义                      |
	|--------------------------|------------------------------------------------|
	| `-XX:+/-<option>`        | 是否开启                                       |
	| `-XX:<option>=<number>`  | 设置数值，可使用 M/K/G 等单位<br>如 32k= 32768 |
	| `-XX:<option>=<string>,` | 设置文本，用于指定文件、路径或命令列表         |

## 常用

| 类型                       | 参数                                    | 描述                                                              |
|----------------------------|-----------------------------------------|-------------------------------------------------------------------|
| 分配                       |                                         |                                                                   |
|                            | `-Xms`                                  | 堆的最小空间大小                                                  |
|                            | `-Xmx`                                  | 堆的最大空间大小<br>建议与 `-Xms` 一致避免收缩、增大带来的性能问题 |
|                            | `-Xmn`<br>`-XX:NewSize`                 | 新生代最小空间大小                                                |
|                            | `-XX:MaxNewSize`                        | 新生代最大空间大小                                                |
|                            | `-XX:PermSize`                          | 永久代最小空间大小                                                |
|                            | `-XX:MaxPermSize`                       | 永久代最大空间大小                                                |
|                            | `-Xss`                                  | 每个线程的堆栈大小                                                |
| 行为                       |                                         |                                                                   |
|                            | `-XX:-UseParallelGC`                    | 使用并行垃圾回收器                                                |
|                            | `-XX:-UseParallelOldGC`                 | Full GC 时使用并行垃圾回收器                                      |
|                            | `-XX:-UseSerialGC`                      | 使用串行垃圾回收器                                                |
| G1 回收器<br>Garbage First |                                         |                                                                   |
|                            | `-XX:+UseG1GC`                          | 使用 G1 收集器<br>将堆切分为大小一                                |
|                            | `-XX:InitiatingHeapOccupancyPercent=45` | 触发 Gc 的 Heap 占用比例                                          |
|                            | `-XX:NewRatio=2`                        | old/ new                                                          |
|                            | `-XX:SurvivorRatio=8`                   | eden/ survivor                                                    |
|                            | `-XX:G1ReservePercent=10`               | 保留堆比例值<br>减少分配分配的可能性                              |
|                            | `-XX:G1HeapRegionSize={1M,32M}`         | 堆的切分大小值                                                    |
|                            | `-XX:MaxTenuringThreshold=15`           | 指定经过过多少次 Young GC 后才转到老生代                          |
| 性能                       |                                         |                                                                   |
|                            | `-XX:LargePageSizeInBytes=4m`           | 配合 `-XX:+UseLargePages` 使用                                    |
|                            | `-XX:MaxHeapFreeRatio=70`               | GC 后的堆空闲的最大比例<br>避免收缩                               |
|                            | `-XX:NewRatio=2`                        | old/ new                                                          |
|                            | `-XX:SurvivorRatio=8`                   | eden/ survivor                                                    |
|                            | `-XX:NewSize=2m`                        | 新生代默认大小                                                    |
|                            | `-XX:MaxPermSize=64m`                   |                                                                   |
|                            | `-XX:TargetSurvivorRatio=50`            | 清除后的幸存区使用比例                                            |
|                            | `-XX:ThreadStackSize=512`               | 线程栈的大小                                                      |
| 调试                       |                                         |                                                                   |
|                            | `-XX:-CITime`                           | 打印 JIT 编译器花费的时间                                         |
|                            | `-XX:ErrorFile=<path>`                  | 异常打印至指定文件                                                |
|                            | `-XX:-HeapDumpOnOutOfMemoryError`       | 内存不足时自动 dump                                               |
|                            | `-XX:HeapDumpPath<path>`                | 堆 dump 保存对指定文件                                            |
|                            | `-XX:-PrintClassHistogram`              | 打印类的直方图                                                    |
|                            | `-XX:-PrintConcurrentLocks`             | 打印 Dump 中的并发锁信息                                          |
|                            | `-XX:-PrintGC`                          | 打印垃圾回收的信息                                                |
|                            | `-XX:-PrintGCDetails`                   | 打印垃圾回收的细节信息                                            |
|                            | `-XX:-PrintTenuringDistribution`        | 打印老年代的年龄段分布情况                                        |
|                            | `-XX:-TraceClassLoading`                | 追踪类的加载                                                      |
|                            | `-XX:-TraceClassLoadingPreorder`        | 追踪所有类的加载顺序                                              |
|                            | `-XX:-TraceClassResolution`             | 追踪常量池的情况                                                  |
|                            | `-XX:-TraceClassUnloading`              | 追踪类的卸载                                                      |
|                            | `-XX:-TraceLoaderConstraints`           | 追踪加载器约束记录                                                |
|                            | `-XX:ParallelGCThreads=n`               | 配置 Heap 分布执行 GC 的线程数                                    |
|                            | `-XX:+UseCompressedOops`                | 使用压缩的 Oops                                                   |
|                            | `-Xloggc:<filename>`                    | 输出 GC 的详细信息至指定文件                                      |


# 补充
- JDK 1.8 使用 MetaSpace 代替 Permanent Generation
	- 原因
		- 使用内存管理，减少内存溢出
		- 促进与 JRockit VM 的事例
	- 操作
		- PermGen 移除
		- File & Class 与 Code 移动至 metaSpace
		- Class Constatnt Pool 移动至 Java.Heap

- 比例
	- EdenSpace: FromSpace: ToSpace= 8: 1: 1

- 别名
	- Heap
		- GC 堆
	- Method Area
		- Non-heap
		- 永久层
	- Thread.Native Methos Stack
		C 栈

- GC
	- Heap
		- 对象
	- Method Area
		- 常量池
		- 类型卸载

- 基本数据类型
	- boolean
	- byte
	- char
	- short
	- int
	- long
	- float
	- double

- 局部变量表
	- 基本数据类型
	- 对象引用
	- returnAddress(下一字节码指令地址)

- 操作数栈
	- FIFO 栈
	- 最大深度于编译时确定，并于方法和栈帧关联时提供
	- 数值来源为常量、本地参数值或字段
		- 亦保存操作结果至栈顶
		- 递归方法调用

- 动态链接
	- 于栈帧中，由运行时常量池的引用，作为当前方法用于提供方法代码的动态链接的类型
	- 方法的类文件代码，引用要执行的方法，并通过符号引用来访问变量
		- 动态链接将符号方法引用至具体的方法引用，将变量定位至存储处的指定偏移位置



# 参考
- [JVM内存结构](https://www.cnblogs.com/ityouknow/p/5610232.html)
	- 结构配图清晰明了
- [The Structure of the Java Virtual Machine](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html)
- [JAVA的内存模型及结构](http://ifeve.com/under-the-hood-runtime-data-areas-javas-memory-model/)
- [JVM内存区域详解](https://blog.csdn.net/shiyong1949/article/details/52585256)
- [JVM 优化经验总结](https://www.ibm.com/developerworks/cn/java/j-lo-jvm-optimize-experience/index.html)
	- JVM 参数入门
- [某应用的GC调优总结](https://blueswind8306.iteye.com/blog/1194438)
- [快速解读GC日志](https://blog.csdn.net/renfufei/article/details/49230943)
	- 针对 GC 日志各字段进行解析
- [Java HotSpot VM Options](https://www.oracle.com/technetwork/articles/java/vmoptions-jsp-140102.html)
- [JVM参数设置详解](http://www.51testing.com/html/38/225738-213696.html)
- [深入探究 JVM | 探秘 Metaspace](https://www.sczyh30.com/posts/Java/jvm-metaspace/)
	- JDK 由 MetaSpace 代替 PermGen 的原因
- [JEP 122: Remove the Permanent Generation](http://openjdk.java.net/jeps/122)
	- 官方回答
- [最简单例子图解JVM内存分配和回收](http://ifeve.com/a-simple-example-demo-jvm-allocation-and-gc/)
