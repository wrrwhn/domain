# 重点
- 线程由**自己决定**是否停止，而不应由其它线程来强制中断或停止
    - 线程可以根据中断检测，来决定终止，等待新任务或继续执行
    - stop/suspend/ resume 因此被废弃

# 作用
- 调用 `interrupt()`

| 线程状态           | 操作                                                                                  |
|--------------------|-------------------------------------------------------------------------------------|
| BLOCKED<br>WAITING | 退出阻塞状态<br>抛出 `InterruptedException`<br>抛出完成后立即将中断标记重置为 `false` |
| RUNNABLE           | 仅将线程的中断标志设置为 `true`                                                       |

# 注意
- **获取锁的过程不可中断**
    - 在 `synchronized` 和 `reentrantLock.lock()` 时，可能因为无法终止而产生死锁
    - 推荐使用 `reentrantLock.tryLock(timeout,unit)` 或 `reentrantLock.lockInterruptibly()` 来保障中断状态的输出
- **底层**业务，不建议捕获 `InterruptedException`，直接**抛出**
- 针对流，在实现 `interruptibleChannel` 接口的基础上，可通过 `close()` 将资源关闭，放开相应阻塞

# 写法
## 中断线程

```java
// 识别中断标识，自行决定中断场景如何操作
while(!Thread.currentThread().isInterrupted() && ...){
    try{
        ...
    } catch(InterruptedException e){
        // 针对阻塞场景时，中断阻塞后，更新中断标识 
        Thread.currentThread().interrupt();
    }
}
```

## 底层中断

```java
    // 建议直接抛出
    void xxx() throws InterruptedException {...}
```

# 参考
- [Thread的中断机制(interrupt)](https://www.cnblogs.com/onlywujun/p/3565082.html)
    - 内容详尽，推荐！
- [Java里一个线程调用了Thread.interrupt()到底意味着什么？](https://www.zhihu.com/question/41048032)
