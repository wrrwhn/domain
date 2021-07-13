---
title: "Book.JVM.线程安全"
date: "2020-05-06"
categories:
 - "阅读"
tags:
 - "Java"
 - "JVM"
toc: true
---

> 加锁，其实就是为了实现线程安全

## 何为线程安全
> 当多个线程访问一个对象时，如果不用考虑这些线程在运行时环境下的调度和交替执行，也不需要进行额外的同步，或者在调用方进行任何的其他的协调操作，调用这个对象的行为都可以获得正确的结果，那这个对象是线程安全的。 —— 《Java Concurrency Practice》

- 此处的线程安全，限定于**多个线程之间存在共享访问的数据**

## 线程安全的实现方式

### 互斥同步（悲观锁）
> 多个线程并发访问数据时，保证共享数据在同一时间有且只能被一个线程所使用
- synchronized（重量级锁）
    - 通过对对象的锁定，实现对某块代码的抢占式（互斥）使用
    - 各场景下的锁定对象和加锁方式
        - 方法块
            - -> 实例对象
            - 在代码块前后添加 monitorenter 和 monitorexit 字节码指令，实现对 reference 类型对象的锁定和解锁
        - 类方法
            - -> 当前类的实例(this)
            - 调用方法时，检测到 ACC_SYNCHRONIZED 标志被设置情况，线程将尝试获取当前类实例的 ObjectMonitor 的所有权
        - 静态方法-> 当前类的 class 对象
            - 调用方法时，检测到 ACC_STATIC 和 ACC_SYNCHRONIZED 标志均被设置时，线程将尝试获取当前类的 Class 对象的 ObjectMonitor 所有权
    - 更多原理，详见扩展

- ReenterLock
    - 与 synchronized 类型，支持线程重入特性，通过 API 层面的互斥锁（try-finally+ lock()+ unlock()）来实现
    - 此外，增加了诸多特性
        - 等待可中断
            - 持有锁线程占有时间过长时，可主动放弃等待
        - 可实现公平锁
            - 多线程等待同一锁时，按申请锁的时间顺序依次获取
            - 默认为非公平锁
        - 绑定多个条件
            - 可通过多个 Condition 对象，区别于 synchronized 仅支持对锁对象的 wait/ notify/ notifyAll 操作
    - JDK 1.6 版本已进行锁优化，效率与 synchronized 基本持平，可以考虑
        - 但性能优化上更偏向于 synchronized，优先考虑

### 非阻塞同步（乐观锁）
> 基于冲突检测的乐观并发；先行更新，没有其它线程竞争，则操作成功；而发生冲突时，则采用重试等策略直至成功。其中通过硬件来保障数据操作与冲突检测这两个步骤具有原子性
- 测试并设置（Test-and-Set）
- 获取并增加（Fetch-and-Increment）
- 交换（Swap）
- **比较并交换（Compare-and-swap，简称 CAS）**
- 加载链接/条件存储（Load-Linked/Store-Conditional，简称 LL/SC）

### 无同步方案
> 同步只是手段，但如果能将方法调整为不涉及共享数据，则自然无须同步措施来保障运行的准备性
- 可重入代码
    - 代码执行的任意时刻被中断、重新执行后，执行结果不会出现任何错误
- 线程本地存储
    - 将共享数据的可见范围，限定于同一线程内，即可避免线程之间的数据竞争

## 锁优化
### 自旋锁
- 当线程申请锁时，锁被占用，则让当前线程执行一个忙循环（自旋），看看持有锁的线程是否会很快释放锁。如果自旋后还没获得锁，才进入同步阻塞状态
- 默认值为 10 次，可有效地避免线程切换的开销

### 自适应自旋锁
- 由前一次在同一个锁上的自旋时间以及锁拥有者的状态来决定。
- 如果在同一个锁对象上, 自旋等待刚好成功获得锁， 并且在持有锁的线程在运行中， 那么虚拟机就会认为这次自旋也是很有可能获得锁， 进而它将允许自旋等待相对更长的时间，如 100 次。

### 锁消除
- 根据代码逃逸技术，如果判断到一段代码中，堆上的数据不会逃逸出当前线程（即不会影响线程空间外的数据），那么可以认为这段代码是线程安全的，不必要加锁
- 如 JDK 1.5 以后进行字符串拼接
    - StringBuffer.append() 为 synchronized 修饰的方法

### 锁粗化
- 在一个间隔性地需要执行同步语句的线程中，如果在不连续的同步块间频繁加锁解锁是很耗性能的，因此把加锁范围扩大，把这些不连续的同步语句进行一次性加锁解锁
- 如连续多次地调用 StringBuffer.append()

### 轻量级锁
- 在没有多线程抢占时，减少使用互斥量的性能损耗
    - 基于“绝大部分锁，在整个同步期间内都是不存在竞争的”
- 实现原理
    - 检测 Object.Header.MarkWord 是否为 01（未被锁定）
    - 拷贝 Object.Header.MarkWord 至线程栈中新增的 Lock record.Displaced Mark Word 区
    - 尝试使用 CAS 方式将 Object.Header.MarkWord 指向该线程栈的 Lock record
        - 成功则该线程拥有锁，继续执行
        - 失败时检测 Object.Header.MarkWord 是否指向该进程，是则说明已持有锁；否则将膨胀为重量级锁
- 图示
    - ![线程安全-轻量级锁.png](http://doc.yqjdcyy.com/36b72e5d-4db7-4866-956d-150b2cb12fef.png)

### 偏向锁
- 在没有多线程抢占时，假定后续也不会有其它线程访问，偏向于每个执行它的线程
    - 执行过程中，该锁未被其它线程获取，则持有偏向锁的线程不需要再进行同步
- 实现原理
    - 锁对象第一次被获取时，CAS 将获取锁的线程 ID 记录至 Object.Header.MarkWord 中
        - 操作成功，则后续线程进入锁定的代码区，JVM 将不再需要任何同步操作
    - 当有另一线程尝试获取锁时，将根据对象当前锁定状态来变更锁状态
        - 当有其它线程竞争偏向锁时，持有偏向锁的进程才会释放锁
            - 撤销时机为全局安全点（无字节码执行）
        - 暂停持有偏向锁的线程，并根据当前锁对象的锁定状态，决定将 Object.Header.MarkWord 切换到无锁（01）或轻量锁（00）状态
- 图示
    - ![线程安全-偏向锁.png](http://doc.yqjdcyy.com/a19f537d-3930-46f5-a3e1-b2f331c07a83.png)

## 扩展
### 互斥的方式（先挖不填）
- 临界区 Critical Section
- 互斥量 Mutex
- 信号量 Semaphore

### synchronized 的实现原理
- 编译结果
    - 方法块
        - ![synchronized-block.png](http://doc.yqjdcyy.com/270f76b6-8a04-403a-875f-19d3b1597535.png)

    - 类方法
        - ![synchronized-method.png](http://doc.yqjdcyy.com/3a78c12e-7d31-4366-a548-f0bd5785f405.png)

    - 静态方法
        - ![synchronized-static-method.png](http://doc.yqjdcyy.com/340bd1e1-9f50-43e3-aceb-183b95d8715e.png)

- 指令解析
    - monitorenter/ monitorexit
        - 执行monitorenter指令理解为加锁，执行monitorexit理解为释放锁。 
        - 每个对象维护着一个记录着被锁次数的计数器。未被锁定的对象的该计数器为0，当一个线程获得锁（执行monitorenter）后，该计数器自增变为 1 ，当同一个线程再次获得该对象的锁的时候，计数器再次自增。当同一个线程释放锁（执行monitorexit指令）的时候，计数器再自减。当计数器为0的时候。锁将被释放，其他线程便可以获得锁。
    - ACC_SYNCHRONIZED
        - 方法级的同步是隐式的。同步方法的常量池中会有一个ACC_SYNCHRONIZED标志。
        - 当某个线程要访问某个方法的时候，会检查是否有ACC_SYNCHRONIZED，如果有设置，则需要先获得监视器锁，然后开始执行方法，方法执行之后再释放监视器锁

- 通过 ObjectMonitor 实现
    - 对象结构（简化版本）
        - count/ owner/ enterlist/ waitset
    - 状态转换流程
        - ![synchronized-howto.png](http://doc.yqjdcyy.com/5af3ba09-7f33-40e7-8eeb-599fb93baf9b.png)

    - 转换图示
        - ![synchronized-howto-image.png](http://doc.yqjdcyy.com/9348e830-3908-4beb-8752-e1c5c2128d13.png)

- 关联
    - synchronized 锁定后，Object.Header.MarkWord 将指向 ObjectMonitor 对象
    - 更多细节详见 《深入理解 Java 虚拟机》 章节 2.3.2


## 参考
- 《深入理解 Java 虚拟机》 
    - 第 13 章 线程安全与锁优化（必读，细节拉满）
- [深入分析synchronized原理和锁膨胀过程](https://www.lagou.com/lgeduarticle/63370.html)
- [Synchronized解析——如果你愿意一层一层剥开我的心](https://juejin.im/post/5d5374076fb9a06ac76da894)
    - Synchronized 原理递进（推荐)
- [Synchronized的实现原理](https://www.hollischuang.com/archives/1883)
    - 反编译结果解析和相应原文分享
- [Moniter的实现原理](https://www.hollischuang.com/archives/2030)
    - ObjectMonitor 实现分享
- [不可不说的Java“锁”事](https://tech.meituan.com/2018/11/15/java-lock.html)
    - 从其它角度切换（推荐）