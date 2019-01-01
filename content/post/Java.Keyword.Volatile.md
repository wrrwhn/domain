---
title: "Java.Keyword.Volatile"
date: "2018-12-31"
categories:
 - "整理"
tags:
 - "Java"
 - "Thread"
 - "Keyword"
toc: true
---

# 作用
- 原子性
	- 对修饰变量的单次读/ 写操作支持
		- double/ long 数据由高低32位组成，导致操作非原子性
	- 不保证 `i++` 等操作的原子性
		- 本质是读+写两次操作

- 可见性
	- 支持 JMM 中的可见性
		- 多线程时对同一变量进行操作，某一进程对其所做的修改，都应立马被其它线程可见

- 有序性
	- 针对单例模式情况下，对单例对象添加修饰以保障执行顺序正常

# 原理

## 可见性
- 线程使用的数据来自于主存，缓存于线程内存中
	- 修改后，将线程内存值**刷新至主存**
	- 更新引用该值的**其它线程**的，线程内存中的该值**无效**

## 有序性
- 针对处理器排序，使用内存屏障保障执行结果的正确
	- **写之后插入 storestore 屏障**
	- **读之前插入 loadload 屏障**
- 针对 JIT 编译重排序，根据 **happen-before** 规则进行优化

# 适合场景
- 状态位
    - 多线程的逻辑状态判断位
- 双重检查
    - 单例模式
- 低开销读写锁

    ```java
    private volatile int value;
    public synchronized int getValue(){return value;}
    ```

# 补充

## Happen-Before
- 规则
	- 官方版本

		```
		Each action in a thread happens before every subsequent action in that thread. 
		An unlock on a monitor happens before every subsequent lock on that monitor. 
		A write to a volatile field happens before every subsequent read of that volatile. 
		A call to start() on a thread happens before any actions in the started thread. 
		All actions in a thread happen before any other thread successfully returns from a join() on that thread. 
		If an action a happens before an action b, and b happens before an action c, then a happens before c.
		```

	- 理解
		- 保障**同一线程**内动作执行的顺序
		- 针对**同一 monitor**，解锁前动作先于加锁动作
		- 针对 **volatile** 所修饰的字段，写操作先于之后的任意读操作
		- 线程的 **start()** 的执行先于该线程内的任一动作
		- 线程操作先于通过 **join()** 方式加入的其它线程
		- 优先顺序具有**传递性**，如 a< b, b< c 则 a< c

## as-if-serial
- 不管编译器或处理器如何重排，都不能影响程序执行结果的正确性

## 内存屏障
- 作用
	- 用于避免 CPU 重排序对执行结果正确性的影响

- 类型

	|         类型         |            示例            |                     描述                     |
	|----------------------|----------------------------|----------------------------------------------|
	| LoadLoad<br>lfence   | load-A;LoadLoad;Load-B     | 确保在B 加载之前，A能先加载完成              |
	| StoreStore<br>sfence | Store-A;StoreStore;Store-B |                                              |
	| LoadStore            | Load-A;LoadStore;Store-B   |                                              |
	| StoreLoad<br>mfence  | Store-A;StoreLoad;Load-B   | **开销最大**<br>兼具前三者功能，又名全能屏障 |

- 规则
	- JSR-133 排序规则
	- ![CPU-Fence.png](http://doc.yqjdcyy.com/17e1f29a-9ceb-4fba-9ac4-2808dca57af2.png)


# 参考
- [JVM内存模型、指令重排、内存屏障概念解析](https://www.cnblogs.com/chenyangyao/p/5269622.html)
- [一文解决内存屏障](https://monkeysayhi.github.io/2017/12/28/%E4%B8%80%E6%96%87%E8%A7%A3%E5%86%B3%E5%86%85%E5%AD%98%E5%B1%8F%E9%9A%9C/)
- [谈乱序执行和内存屏障](https://blog.csdn.net/dd864140130/article/details/56494925)
	- `as-if-serial` 原则
- [Java内存模型Cookbook（二）内存屏障](http://ifeve.com/jmm-cookbook-mb/)
	- 内存屏障的事例
    - 补充有原子操作情况下的屏障事例
- [Java 并发编程：volatile的使用及其原理](http://www.cnblogs.com/paddix/p/5428507.html)
- [深入分析volatile的实现原理](http://cmsblogs.com/?p=2092)
- [全面理解Java内存模型(JMM)及volatile关键字](https://blog.csdn.net/javazejian/article/details/72772461)
    - 重排序等事例详尽
- [正确使用 Volatile 变量](https://www.ibm.com/developerworks/cn/java/j-jtp06197.html)