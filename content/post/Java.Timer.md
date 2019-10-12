---
title: "Java.Timer"
date: "2019-10-12"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---

> 定时任务工具类，支持任务的单次或多次重复执行  
> 由 `Timer` 根据配置调用 `TimerTask` 的线程启动并执行  
> 支持按固定速率或延迟来执行任务  
> 间隔执行情况下，受上一任务执行情况影响（适用于短频重复)
>> 如任务于 10：00 执行，间隔分钟调用，但由于 GC 10:05 分时执行推迟至 10:06，则下一次执行的时间为 10:11 而非预计的 10：10   
 
# 原理
- 定时机制
    - 使用 `Object.wait` 实现，无法保证实时性
        - 影响情况
            - 大量线程等待执行
            - GC 时的 STW
    - 针对重复执行的任务场景，推荐使用 `ScheduledThreadPoolExecutor`
- 回收时机
    - 自动回收（时间无法保证）
        - Timer 无强引用
        - 且任务均已执行完成时
    - 手动回收（线程安全）
        - 使用 `Timer.cancel`
        - 如若使用 `stop`，会导致定时任务报 `IllegalStateException` 异常

# 缺点
- 执行线程仅一个
    - 任务执行时间过长，会影响其它任务执行的精确性
- 异常即路上
    - 执行的任务出现异常，执行线程就会中止，影响了其它任务的执行
- 依赖系统时间
    - 系统时间的修改，影响任务的正常执行

# 构架图
- ![Timer-func.png](http://doc.yqjdcyy.com/63e37941-f401-49d5-a58c-14393b6935b6.png)


# 源码

## TimerTAsk
### 特点
- 可供 Timer 调用的 Runnable 封装

### 状态

| 状态      | 数值 | 含义                                     |
|-----------|------|----------------------------------------|
| VIRGIN    | 0    | 尚未被执行                               |
| SCHEDULED | 1    | 重复任务                                 |
| EXECUTED  | 2    | 非重复任务执行完成，且未被取消            |
| CANCELLED | 3    | 取消状态<br>调用 `TimerTask.cancel` 触发 |

### 属性

- `final Object lock`
    - 内部访问锁

- `int state`
    - 任务状态

- `long nextExecutionTime`
    - 任务下次执行的时间
    - 重复任务，会重复更新该字段

- `long period`
    - 重复任务的执行间隔时间
    - 数值含义

        | 数值 | 含义                             |
        |------|--------------------------------|
        | >0   | 时间敏感，固定时间点执行          |
        | =0   | 非重复任务                       |
        | <0   | 固定时间间隔执行<br>调用次数小于预期 |

### 方法
- `boolean cancel()`
    - 将任务的状态更新为取消状态
    - 幂等操作

- `long scheduledExecutionTime()`
    - 特点
        - 任务执行时调用，返回的为当前执行任务的时间
        - 针对 `fixed-rate` 类型任务
            - 因为 `fixed-delay` 允许时间偏移
    - 代码
        ```java
        synchronized(lock) {
            return (period < 0 ? nextExecutionTime + period : nextExecutionTime - period);
        }        
        ```


## TaskQueue
> TimeTask 的优先队列，使用数据实现小堆排序，允许重复元素  
> 删除/添加/查找可达 log(n)

### 属性
- `TimerTask[] queue`
    - 任务队列，小堆排序
        - queue[n]的左右子节点分别为 queue[2n] 和 queue[2n+1]
        - 子节点大于等于父节点
    - 初始 128，每次拓展翻倍
- `int size`

### 方法
- `removeMin()`
    - 描述
        - 将末尾元素（最大)覆盖顶点后，并将末尾元素清空
        - 由顶向下比较，并与较小值更换位置
    - 代码
        ```java
        queue[1] = queue[size];
        queue[size--] = null;
        fixDown(1);
        ```
- `rescheduleMin(long newTime)`
    - 描述
        - 针对重复任务使用
        - 执行完成后，更新至下次执行时间，并进行重新排序 
    - 代码
        ```java
        queue[1].nextExecutionTime = newTime;
        fixDown(1);
        ```
- `fixDown(int k)`
    - 描述
        - 将 k 节点下移至，大于任意节点
    - 代码
        ```java
        int j;
        while ((j = k << 1) <= size && j > 0) {
            // 左右节点取小者
            if (j < size && queue[j].nextExecutionTime > queue[j+1].nextExecutionTime)
                j++;
            // 当前节点数值
            if (queue[k].nextExecutionTime <= queue[j].nextExecutionTime)
                break;
            TimerTask tmp = queue[j];  queue[j] = queue[k]; queue[k] = tmp;
            k = j;
        }
        ```
- `heapify()`
    - 描述
        - 整树重排
    - 代码
        ```java
        for (int i = size/2; i >= 1; i--)
            fixDown(i);
        ```

## TimerThread
> Timer 任务执行辅助线程  
> 共享 Timer 的 TaskQueue，来控制任务的执行/重复调用/完成任务移除

### 属性
- `boolean newTasksMayBeScheduled`
    - 是否还有任务的标记，默认为 true
    - 当标记为 true 且 taskQueue 中无任务时，Timer 将被优雅中断
- `TaskQueue queue`
    - 共享至 Timer 的任务队列

### 方法

- `mainLoop()`
    - 代码
        ```java
        while (true) {
            try {
                TimerTask task;
                boolean taskFired;
                synchronized(queue) {
                    // 无数据时休眠，等待任务插入以唤醒
                    while (queue.isEmpty() && newTasksMayBeScheduled)
                        queue.wait();
                    // 唤醒仍无数据，异常情况
                    if (queue.isEmpty())
                        break; 

                    // 获取最小元素
                    long currentTime, executionTime;
                    task = queue.getMin();
                    synchronized(task.lock) {
                        // 已取消任务，直接移除并重试
                        if (task.state == TimerTask.CANCELLED) {
                            queue.removeMin();
                            continue;
                        }
                        currentTime = System.currentTimeMillis();
                        executionTime = task.nextExecutionTime;
                        if (taskFired = (executionTime<=currentTime)) {
                            // 一次性任务
                            if (task.period == 0) {
                                queue.removeMin();
                                task.state = TimerTask.EXECUTED;
                            } else {
                            // 重置周期任务的下一执行时间
                            // 其中 fixed-delay 直接使用当前时间，而 fixed-rate 情况则使用上一次的执行时间
                                queue.rescheduleMin(
                                  task.period<0 ? currentTime   - task.period
                                                : executionTime + task.period);
                            }
                        }
                    }
                    // 未到时间，直接休眠相应时长
                    if (!taskFired)
                        queue.wait(executionTime - currentTime);
                }
                // 未执行则直接执行
                if (taskFired)
                    task.run();
            } catch(InterruptedException e) {
            }
        }
        ```

## Timer

### 属性
- `final TaskQueue queue`
    - 任务队列
- `final TimerThread thread`
    - 任务调用类
- `final Object threadReaper`
    - Timer 销毁时，GC 调用其 finalizer 来中断 TimerThread 进程
- `static AtomicInteger nextSerialNumber`
    - 用于生成唯一名称

### 构造
- `Timer()`
    - 默认使用 nextSerialNumber 生成唯一名称
- `Timer(boolean isDaemon)`
    - 是否设置为守护进程
    - 与应用执行周期一致，不会延长应用的生命周期
- `Timer(String name)`
    - 指定进行名称
- `Timer(String name, boolean isDaemon)`

### 方法
- `schedule(TimerTask task, long delay| Date time [long period])`
    - 描述
        - 指定任务的执行时间和是否按某一间隔周期调用
    - 代码
        ```java
        sched(task, System.currentTimeMillis()+delay, -period)
        ```

- `scheduleAtFixedRate(TimerTask task, long delay| Date time, long period)`
    - 描述
        - 以固定时间进行任务调用
        - 区别于 `schedule`， `period` 是指两个周期任务之间的间隔
    - 代码
        ```java
        sched(task, System.currentTimeMillis()+delay, period)
        ```
- `sched(TimerTask task, long time, long period)`
    - 描述
        - 统一的调用实现
    - 代码
        ```java
        if (time < 0)
            throw new IllegalArgumentException("Illegal execution time.");

        // 防止周期溢出，且能支持无穷大
        if (Math.abs(period) > (Long.MAX_VALUE >> 1))
            period >>= 1;

        synchronized(queue) {
            if (!thread.newTasksMayBeScheduled)
                throw new IllegalStateException("Timer already cancelled.");

            synchronized(task.lock) {
                if (task.state != TimerTask.VIRGIN)
                    throw new IllegalStateException(
                        "Task already scheduled or cancelled");
                task.nextExecutionTime = time;
                task.period = period;
                task.state = TimerTask.SCHEDULED;
            }

            // 如若当前数据插入至队首，则唤醒进程进行处理或重新等待相应时差
            queue.add(task);
            if (queue.getMin() == task)
                queue.notify();
        }
        ```
- `cancel()`
    - 描述
        - 重置活动标志，并唤醒进程检测跳出
    - 代码
        ```java
        synchronized(queue) {
            thread.newTasksMayBeScheduled = false;
            queue.clear();
            queue.notify();
        }
        ```
- `purge()`
    - 描述
        - 快速清除所有 CANCELLED 状态节点，并全排序
    - 代码
        ```java
        int result = 0;

        synchronized(queue) {
            for (int i = queue.size(); i > 0; i--) {
                if (queue.get(i).state == TimerTask.CANCELLED) {
                    // 快速清除，不排序 
                    queue.quickRemove(i);
                    result++;
                }
            }

            // 有移除则进行全排序
            if (result != 0)
                queue.heapify();
        }

        return result;
        ```

# 参考
- [Timer和TimeTask简介](http://swiftlet.net/archives/645)
- [细说java.util.Timer](https://blog.csdn.net/mhmyqn/article/details/48070879)
- [深入 Java Timer 定时调度器实现原理](https://juejin.im/post/5c17478df265da615b715e5f)
- [Timer和TimeTask简介](http://swiftlet.net/archives/645)
    - 补充了中止的有一种情况
        - timer.cancel
        - daemon 运行，任务完成即停止
        - System.exit 退出