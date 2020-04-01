---
title: "Java.ScheduledThreadPoolExecutor"
date: "2019-10-12"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---

> 支持指令于一定时延或周期性调用的 ExecutorService，创建后返回任务对象以便于校验或取消  
> 不建议配置 `corePoolSize` 为0 或使用 `allowCoreThreadTimeOut`,因为可能导致没有线程来执行任务  

# 原理

# 架构图
- ![ScheduledThreadPoolExecutor.png](http://doc.yqjdcyy.com/ab030e7a-2106-4c4f-aa11-a6cc0a5993ae.png)


# 源码 

## Executor
> Runnable 任务的提交，支持任务的解耦

### 方法
- `execute(Runnable command)`
    - 描述
        - 于未来某时执行指定的任务，可于新任务中，线程池或当前调用线程中

## ExecutorService
> 提供中止和封装 Future（用于跟踪多个异常任务的进度） 的工厂方法


### 方法
- `shutdown()`
    - 描述
        - 有序地关闭之前执行的任务，并且不再接收新的任务
        - 不等待执行中的任务完成执行
- `List<Runnable> shutdownNow()`
    - 描述
        - 停止执行中的任务，中断等待执行的任务并返回
        - 不能保障为最佳实践，因为实现中一般使用 `Thread.interrupt` 来中断，但需要任务中有进行相应的处理
- `boolean awaitTermination(long timeout, TimeUnit unit)`
    - 描述
        - 等待任务中断完成/线程中断/等待达到 timeout.unit 时长中任一条件达成
- `Future<T> submit(Callable<T>|Runnable test[, T result])`
    - 描述
        - 提交带返回值（可选，默认为0）的任务
        - 返回 Future 来跟进任务的执行
- `List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks[,long timeout, TimeUnit unit])`
    - 描述
        - 批量执行所有任务，并等待其完成【或超时任一条件】触发
        - Future 与列表中的任务按顺序对应
- `List<Future<T>> invokeAny(Collection<? extends Callable<T>> tasks[,long timeout, TimeUnit unit])`
    - 描述
        - 执行所有任务，直到任一任务完成【或超时任一条件】触发
            - 任务完成指代执行完毕，非抛出异常情况
            - 取消所有未完成或抛出异常的任务

## FutureTask
> 可取消的异步计算任务
> 支持单个计算线程，和多个数据获取的线程（通过 get 将当前请求线程阻塞，并存入 waiters 栈中）

### 属性
- `volatile int state`
    - 任务执行阶段
    - 含义

        | 名称         | 数值 |
        |--------------|------|
        | NEW          | 0    |
        | COMPLETING   | 1    |
        | NORMAL       | 2    |
        | EXCEPTIONAL  | 3    |
        | CANCELLED    | 4    |
        | INTERRUPTING | 5    |
        | INTERRUPTED  | 6    |

- `Callable<V> callable`
    - 计算函数

- `Object outcome`
    - 返回的计算结果或异常对象

- `volatile Thread runner`
    - 当前执行计算函数的线程

- `volatile WaitNode waiters`
    - 栈，存储了等待计算结果的线程

### 构造
- `FutureTask(Runnable runnable, V result)`
    - 通过 `Executors.callable(runnable, result)` 将 `Runnable` 封装为 `Callable`

### 方法
- `boolean cancel(boolean mayInterruptIfRunning)`
    - 描述
        - 取消当前任务
        - 单个执行线程中断，多个监听线程唤醒
    - 代码
        ```java
        // 初始状态而立马更新状态并返回
        if (!(state == NEW && UNSAFE.compareAndSwapInt(this, stateOffset, NEW, mayInterruptIfRunning ? INTERRUPTING : CANCELLED)))
            return false;
        try {    
            // 通过中断方式快速结束任务
            if (mayInterruptIfRunning) {
                try {
                    Thread t = runner;
                    if (t != null)
                        t.interrupt();
                } finally {
                    // 将最终状态更新为【INTERRUPTED】
                    UNSAFE.putOrderedInt(this, stateOffset, INTERRUPTED);
                }
            }
        } finally {
            // 唤醒等待线程，让其读取计算结果
            finishCompletion();
        }
        return true;
        ```

- `finishCompletion()`
    - 描述
        - 遍历并唤醒等待结果的线程
    - 代码
        ```java
        for (WaitNode q; (q = waiters) != null;) {
            if (UNSAFE.compareAndSwapObject(this, waitersOffset, q, null)) {
                for (;;) {
                    Thread t = q.thread;
                    if (t != null) {
                        q.thread = null;
                        // 唤醒等待结果的进程
                        LockSupport.unpark(t);
                    }
                    WaitNode next = q.next;
                    if (next == null)
                        break;
                    // 清空当前节点，帮助其迟早被 GC 回收
                    q.next = null;
                    q = next;
                }
                break;
            }
        }
        // 内部实现版本中，针对完成操作后的相关动作
        done();
        callable = null;
        ```

- `V get()`
    - 描述
        - 阻塞式等待计算的结果
    - 代码
        ```java
        int s = state;
        if (s <= COMPLETING)
            // 等待计算任务完成【或等待时间任一条件完成】
            // 数值为0时，表示一直等待
            s = awaitDone(false, 0L);
        return report(s);
        ```

- `int awaitDone(boolean timed, long nanos)`
    - 描述
        - 等待计算完成，或出现中断/超时
    - 代码
        ```java
        final long deadline = timed ? System.nanoTime() + nanos : 0L;
        WaitNode q = null;
        boolean queued = false;
        for (;;) {
            if (Thread.interrupted()) {
                removeWaiter(q);
                throw new InterruptedException();
            }

            int s = state;
            // 计算完成，则跳出
            if (s > COMPLETING) {
                if (q != null)
                    q.thread = null;
                return s;
            }
            // 已在计算，则让出时间片，优先计算
            else if (s == COMPLETING)
                Thread.yield();
            // 仍处理【新建】状态时，创建等待标志
            else if (q == null)
                q = new WaitNode();
            // 将等待凭证入队
            else if (!queued)
                queued = UNSAFE.compareAndSwapObject(this, waitersOffset, q.next = waiters, q);
            // 线程挂起（若指定等待时间，则挂起相应时长）
            else if (timed) {
                nanos = deadline - System.nanoTime();
                if (nanos <= 0L) {
                    removeWaiter(q);
                    return state;
                }
                LockSupport.parkNanos(this, nanos);
            }
            else
                LockSupport.park(this);
        ```

- `removeWaiter(WaitNode node)`
    - 描述
        - 移除超时或中断的等待节点，以避免内存的占用
    - 代码
        ```java
        if (node != null) {
            node.thread = null;
            // 定义锚点，重复遍历 
            retry:
            for (;;) {
                for (WaitNode pred = null, q = waiters, s; q != null; q = s) {
                    s = q.next;
                    // 当前线程已中断
                    if (q.thread != null)
                        pred = q;
                    // 跳过当前节点
                    else if (pred != null) {
                        pred.next = s;
                        if (pred.thread == null) // check for race
                            continue retry;
                    }
                    // 一路过来尚未碰到非空节点，则将等待节点指向下一节点
                    else if (!UNSAFE.compareAndSwapObject(this, waitersOffset,
                                                          q, s))
                        continue retry;
                }
                break;
            }
        }
        ```

- `run()`
    - 描述
        - 调用计算方法，并输出结果
    - 代码
        ```java
        // 已有线程处理，则跳过
        if (state != NEW || !UNSAFE.compareAndSwapObject(this, runnerOffset, null, Thread.currentThread()))
            return;
        try {
            // 执行并返回结果|异常
            Callable<V> c = callable;
            if (c != null && state == NEW) {
                V result;
                boolean ran;
                try {
                    result = c.call();
                    ran = true;
                } catch (Throwable ex) {
                    result = null;
                    ran = false;
                    setException(ex);
                }
                if (ran)
                    set(result);
            }
        } finally {
            runner = null;
            int s = state;
            if (s >= INTERRUPTING)
                // 针对取消时的状态更新
                handlePossibleCancellationInterrupt(s);
        }
        ```

- `boolean runAndReset()`
    - 描述
        - 执行计算，但不设置返回值
        - 设计于支持多次执行的场景


## AbstractExecutorService
> ExecutorService 的默认实现，主要实现了 submit/ invokeAny/ invokeAll  


### 属性
- ``
    - 

### 构造
- ``
    - 

### 方法
- ``
    - 描述
        - 
    - 代码
        ```java
        ```
## 

### 属性
- ``
    - 

### 构造
- ``
    - 

### 方法
- ``
    - 描述
        - 
    - 代码
        ```java
        ```


# 参考
- []()
- [从FutureTask内部类WaitNode深入浅出分析FutureTask实现原理](http://www.voidcn.com/article/p-gzjtuzut-bom.html)
- [LockSupport原理剖析](https://segmentfault.com/a/1190000008420938 )
- []()
- []()