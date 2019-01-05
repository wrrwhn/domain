---
title: "Java.JVM.GC"
date: "2019-01-05"
categories:
 - "整理"
tags:
 - "Java"
 - "JVM"
toc: true
---

# 存活判断
- 方式

	| 方式       | 描述                                                           | 优点 | 缺点                     |
	|----------|--------------------------------------------------------------|------|--------------------------|
	| 引用计数   | 对象持有被引用的计数值，为0时可被回收                           | 简单 | 无法解决对象相互引用问题 |
	| 可达性分析 | 由 `GC Roots` 开始向下搜索，路径（引用链）中所涉及的即为可达、存活 |      |                          |


# 算法

- 类型

	| 算法                                | 描述                                                             |
	|-----------------------------------|----------------------------------------------------------------|
	| 标记-清除<br>Mark-Sweep             | 标记出所有待回收的对象，后再统一回收                              |
	| 复制<br>Copying                     | 内存平分两块，一块用完后，将存活对象移动至另一块，后清除上一块      |
	| 标记-压缩<br>Mark-Compact           | 标记出所有待回收的对象，后将存活对象往一端移动后，清除其它区域内存 |
	| 分代收集<br>Generational Collection | 针对新生代和老生代`分别设置`收集算法                             |


- 使用精确式GC，构建 `OopMap` 来存储对象的引用
	- 使用`空间换时间`
	- 在`安全点`生成 `OopMap`
	- 在`安全区域`上开始 `GC`


# 垃圾回收器

## 简介

| 回收器                   | 描述                                                                              | 参数                                                                                                                                                                                                                            | 优点                                       | 缺点                             |
|-----------------------|-----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------|----------------------------------|
| Serial 收集器            | `单线程`回收<br>新生代、老年代串行回收<br>新生代复制，老年代标记-压缩               | `-XX:+UseSerialGC` 启用                                                                                                                                                                                                         | `稳定`<br>`效率高`                         | `较长停顿`                       |
| Serial Old 收集器        | Serial 收集器针对`老生代`的版本<br>                                               | `-XX:+UseSerialGC` 启用                                                                                                                                                                                                         |                                            |                                  |
| ParNew 收集器            | Serial 的`多线程`版本<br>新生代并行，老年代串行<br>新生代复制，老年代标记-压缩      | `-XX:+UseParNewGC` 启用<br> `-XX:ParallelGCThreads` 限制线程数                                                                                                                                                                  |                                            |                                  |
| Parallel Scavenge 收集器 | 类似 ParNew 收集器<br>                                                            | `-XX:+UseParallelGC` Parallel+ Serial                                                                                                                                                                                           | `提高吞吐量`                               |                                  |
| Parallel Old 收集器      | Parallel Scavenge 收集器的老年代版本<br>JDK1.6后支持<br>使用`多线程`和`标记-压缩` | `-XX:+UseParallelOldGC` Parallel+ Parallel                                                                                                                                                                                      |                                            |                                  |
| CMS 收集器               | Concurrent Mark Sweep                                                             | `-XX:+UseConcMarkSweepGC` 启用<br> `-XX:+UseCMSCompactAtFullCollection` Full GC后进行一次碎片整理<br>`-XX:+CMSFullGCsBeforeCompaction` 几次 Full GC后进行一次碎片整理<br>`-XX:+XX:ParallelCMSThreads` 设置CMS 线程数（默认CPU数) | `最短的回收停顿`                           | `大量碎片`<br>`并发时吞吐量降低` |
| G1 收集器                | Garbage First<br>针对服务器场景（多核+大内存)                                      | `-XX:+UseG1GC` 启用                                                                                                                                                                                                             | `空间整合`<br>`减少GC停顿`<br>`提高吞吐量` |                                  |

## 详细
### CMS

- 步骤

	| 步骤                             | 描述                                        |
	|--------------------------------|-------------------------------------------|
	| 初始标记<br>CMS initial mark     | `STW`<br>标记 `GC Roots`<br>速度极快        |
	| 并发标记<br>CMS concurrent mark  | `GC Roots Tracing` <br>时间较长             |
	| 重新标记<br>CMS remark           | `STW`<br>修复并发标记时变动的数据<br>速度快 |
	| 并发清除<br>CMS concurrent sweep | 时间较长                                    |


### G1

- 特点
	- 使用标记压缩，**不产生空间碎片**
	- 建立可预测的停顿时间模型，让使用者可**指定消耗在垃圾回收的时间**
	- 细分堆至多个大小一致的区，模糊新生代和老生代的概念
	- **JDK 9 的默认设置**

- 步骤

	| 步骤                              | 描述                                                       |
	|---------------------------------|------------------------------------------------------------|
	| 标记<br>Initial mark              | `STW`<br>触发 Young GC                                     |
	| 根区域扫描<br>Roo region scanning | 在标记完成之前清空 `Survivor` 区                           |
	| 并发标记<br>Concurrent mark       | 在整堆中分区并发进行标记<br>计算各区对象活性（存活对象比例） |
	| 重新标记<br>Remark                | `STW`<br> 使用 `SATB` 算法，较 CMS 快                       |
	| 并发清除<br>Copy-ClenUp           |                                                            |


## 适用

|   代   | 可用垃圾收集器 |
|--------|----------------|
| 年轻代 |                |
|        | Serial         |
|        | ParaNew        |
|        | Parallel       |
|        | G1             |
| 老年代 |                |
|        | Serial Old     |
|        | Parallel Old   |
|        | CMS            |
|        | G1             |


## 使用

- 考量因素
	- 应用程序**场景**
	- **硬件**
	- **吞吐量**

- 场景
	
	| 垃圾回收器 | 场景描述                                                 |
	|------------|------------------------------------------------------|
	| Serial     | 单线程<br>低配置机器                                     |
	| Parallel   | **64bit 服务器默认配置**<br>不要求吞吐量<br>机器性能一般 |
	| CMS        | 以 **CPU 和系统资源**来换取                              |
	| G1         | 针对**堆配置大**的情况<br>**JDK 9 默认配置**             |


## 示例

- Parallel GC
	
	```
	java
		-Xmx3800m
		-Xms3800m
		-Xmn2g
		-Xss128k
			-XX:+UseParallelGC
			-XX:ParallelGCThreads=20
			-XX:+UseParallelOldGC
			-XX:MaxGCPauseMillis=100
			-XX:MaxGCPauseMillis=100
	```


- CMS GC
	
	```
	java
		-Xmx3550m
		-Xms3550m
		-Xmn2g
		-Xss128k
			-XX:ParallelGCThreads=20
			-XX:+UseConcMarkSweepGC
			-XX:+UseParNewGC
			-XX:CMSFullGCsBeforeCompaction=5
			-XX:+UseCMSCompactAtFullCollection
	```


# 工具

- 介绍

	| 工具                              | 作用                                                   |
	|-----------------------------------|------------------------------------------------------|
	| jps<br>JVM Process Status Tool    | 显示系统内所有 HotSpot **虚拟机进程**                  |
	| jstat<br>JVM Statistic Monitoring | 监视**虚拟机运行状态**<br>类装载、内存、垃圾收集、JIT编译 |
	| jmap<br>JVM Memory Map            | 生成 **Heap Dump** 文件                                |
	| jhat<br>JVM Heap Analysis Tool    | **分析** `jmap` 生成的 **dump**，并于浏览器中展示       |
	| jstack<br>JVM Stack Tool          | 生成虚拟机当前的**线程快照**                           |
	| jinfo<br>JVM Configiguration Info | 查看、调整虚拟机**运行参数**                            |


# 补充

## GC Roots

- 作为 GC 可达性遍历的起点
- 有效对象
	- 线程中 **JVM 栈**所引用的对象
	- 类的**静态属性**所引用的对象
	- **常量池**所引用的对象
	- **JNI** 栈中所引用的对象

## 引用类型

- 类型

	| 类型   | 定义                                                                                            | 示例                                                                                                 | 
	|------|-----------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|-----------------|
	| 强引用 | 设置为空，或超出生命周期时才会被回收<br>**关联 GC Roots**                                            | `Object o=new Object();`                                                                             | 
	| 弱引用 | 内存不足时，仅有弱引用指向的对象被回收<br>适用于**内存敏感的高速缓存**                               | `SoftReference`<br>如浏览器的返回页面                                                                | 
	| 软引用 | 不管内存空间足够与否，被垃圾回收器发现便会被回收<br>弱引用所指向的对象无强引用后，获得的值为 null | `WeakReference` 关联对象<br>`WeakHashMap` 键消息时值可**自动清除**<br>`ReferenceQueue`记录自动消失的对象 |
	| 虚引用 | 有跟没有一样<br>仅用于的指向对象被回收后，将自己加入引用对队，**通知关联对象已被销毁的事实**          | `PhantomReference`+  `ReferenceQueue` 结合使用<br>确认销毁，用于大文件加载                            | 


- 生命周期

	| 存活/引用 | 强引用 | 软引用 | 弱引用 | 虚引用 |
	|-----------|--------|--------|--------|--------|
	| GC        | Y      | X      | X      | X      |
	| 回收线程  | Y      | Y      | +1     | X      |

## 缩写
- STW
	- Stop The Word	

-  SATB
	- snapshot at the beginning


# 参考
- [https://segmentfault.com/a/1190000015605327#articleHeader18](https://segmentfault.com/a/1190000015605327)
- [jvm知识点总览](https://zhuanlan.zhihu.com/p/25511795)
- [【证】:那些可作为GC Roots的对象](https://blog.csdn.net/u010798968/article/details/72835255)
	- 验证哪些对象可作为 GC Roots
- [java的gc为什么要分代](https://www.zhihu.com/question/53613423/answer/135743258)
- [Java GC杂谈之对象的可达分析与回收算法](https://blog.csdn.net/wnl_csdn/article/details/73865511)
	- GC Roots与引用的关联
- [十分钟理解Java中的弱引用](http://www.importnew.com/21206.html)
- [Java 7之基础 - 强引用、弱引用、软引用、虚引用](https://blog.csdn.net/mazhimazh/article/details/19752475)
	- 代码示例
- [译文：理解Java中的弱引用](https://droidyue.com/blog/2014/10/12/understanding-weakreference-in-java/)
- [java中的4种reference的差别和使用场景](https://blog.csdn.net/aitangyong/article/details/39453365)
	- 理论+ 代码+ 原理
- [Java Hotspot G1 GC的一些关键技术](https://tech.meituan.com/g1.html)
	- 详细介绍了 G1 的算法、流程、指令和日志
	- 美团出的文章，技术含量确实~
- [Java GC 垃圾回收器的分类和优缺点](https://blog.csdn.net/high2011/article/details/80177473)