---
title: "Java.DelayQueue"
date: "2019-09-26"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---

# DelayQueue
> 无界队列，仅弹出过期的数据

## 结构
- ![DelayQueue.png](http://doc.yqjdcyy.com/6258c4c5-d558-494e-b6ef-714b08b4ea96.png)

## 特点
- Leader-Follwer 模式
    - 无新的队首数据情况下，则不进行唤醒操作
    - 新插入数据为最小优先级数据情况，或 take 操作完成时，唤醒其它线程

## 核心代码
- poll
```java
public E poll() {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        E first = q.peek();             // 队首数据未到期，则直接返回 Null
        if (first == null || first.getDelay(NANOSECONDS) > 0)
            return null;
        else
            return q.poll();
    } finally {
        lock.unlock();
    }
}
```

- take
```java
public E take() throws InterruptedException {
    final ReentrantLock lock = this.lock;       // 可重入锁，代替线程池
    lock.lockInterruptibly();
    try {
        for (;;) {
            E first = q.peek();
            if (first == null)
                available.await();              // 无数据情况下，休眠等待
            else {
                long delay = first.getDelay(NANOSECONDS);
                if (delay <= 0)                 // 队首数据已超时，立即返回
                    return q.poll();
                first = null; 
                if (leader != null)             // 已有 Leader 情况下，则等待唤醒
                    available.await();
                else {
                    Thread thisThread = Thread.currentThread();
                    leader = thisThread;
                    try {                       // **等待队首元素所需要处理的时间**
                        available.awaitNanos(delay);
                    } finally {
                        if (leader == thisThread)
                            leader = null;
                    }
                }
            }
        }
    } finally {
        if (leader == null && q.peek() != null)
            available.signal();
        lock.unlock();
    }
}
```


- offer
```java
public boolean offer(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        q.offer(e);
        if (q.peek() == e) {            // 插入元素位于队首，则直接唤醒线程处理
            leader = null;
            available.signal();
        }
        return true;
    } finally {
        lock.unlock();
    }
}
```


# 依赖
## Queue

### 特点
- FIFO  
- 不允许插入 Null，因为将作为是否失败的标准

### 支持方法
|         | 抛出异常  | 返回特定值              |
|---------|-----------|-------------------------|
| Insert  | add(e)    | offer(e) <br>true/false |
| Remove  | remove()  | poll()<br>value/null    |
| Examine | element() | peak()<br>value         |

## BlockingQueue

### 特点
- **线程安全**
    - 内部锁控制 
- 有限容量
    - 适用于多生产多消费场景
- 内存一致性
    - 插入元素 `happen-before` 访问|移除元素

### 支持方法

|         | 抛出异常  | 返回特定值 | 阻塞   | 超时                  |
|---------|-----------|------------|--------|-----------------------|
| Insert  | add(e)    | offer(e)   | put(e) | offset(e, time, unit) |
| Remove  | remove()  | poll()     | take() | poll(time, unit)      |
| Examine | element() | peek()     | -      | -                     |


## PriorityQueue

### 特点
- 基于 PriorityMap 实现
    - 外在表现无界，但内存维护容量
- 可于初始化指定` Comparator`，否则依赖于其默认排序
- 不允许插入 Null 或无法比较的对象
- **非线程安全**，线程安全的示例可见 `PriorityBlockingQueue`



## Leader-Follwer
- 模式
    - 事件来临时，线程池通知一个 Follwer 线程以成为新的 Leader 去处理该事件，并于完成后返回线程池中
- 优点
    - 消费动态的内存分配（线程的创建与销毁）
    - 减少线程间的数据交换
- 转换
    - ![Pattern-Leader_Follwer.png](http://doc.yqjdcyy.com/4d412667-a01a-41ea-a9d3-44d48c16436e.png)

# 参考
- [精巧好用的DelayQueue](https://www.cnblogs.com/jobs/archive/2007/04/27/730255.html)
- [服务器两种高效的并发模式](https://www.jianshu.com/p/5822b0fa4f2a)
    - 半同步半异步
    - 领导者追随者模式