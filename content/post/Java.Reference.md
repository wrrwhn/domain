---
title: "Java.Reference"
date: "2019-09-12"
categories:
    - "整理"
tags:
    - "Java"
toc: true
---
> 引用的作用概述

# 代码解析

## 类拓扑
- ![java-reference.png](http://doc.yqjdcyy.com/5262bb4a-68a8-46ea-8580-4ab765378e96.png)

- Reference.pending 指向 GC 回收对象的队列队首，而 disdiscovered 指向 pending 状态元素的下一元素
    - pengding 的入队和 discovered 字段的指向操作均由 GC 操作
- ReferenceQueue.head 指向队首元素，元素中 next 指向下一入队元素
    - 由守护进程 ReferenceHandler 操作，数据源为 Reference.pending
- Reference 中启动 ReferenceHandler 守护线程，用于将无引用的元素移动至 pending 列队
- 其中 WeakReference/SoftReference 对象在进入 Queue 后， referent 为 null；而 FinalReference 因为要调用 finalize 方法，所以存在

## 引用指向值

| 类               | 值   | 描述                             |
|------------------|------|--------------------------------|
| PhantomReference | null | 对象本身亦为 null                |
| WeakReference    | null | 清空入队                         |
| SoftReference    | null | 仅于内存不足时，清空入队          |
| FinalReference   | T    | 需保留以用于执行其 finalize 方法 |

## Reference
> 引用类型基类，定义引用对象的通用操作  
> 与 GC 紧密关联，无法直接子类化

### 状态

- 状态列表

| 状态     | 描述                                                                                                           |
|----------|--------------------------------------------------------------------------------------------------------------|
| Active   | 新创建实例的状态<br>后续状态与是否指定有队列有关，有则 Pending 无则 Inactive                                    |
| Pending  | 由 JVM 维护，引用对象被 GC 回收时触发，更新于 Reference.Discovered 字段<br>等待 Reference-handler 线程的入队操作 |
| Enqueued | Reference-handler 线程将元素入队<br>重置元素的队列为 ENQUEUED                                                  |
| Inactive | 不活跃状态，进入后将不再变化                                                                                    |

- Reference 状态转换
    - ![Java.Reference.Status.png](http://doc.yqjdcyy.com/2a6fb349-3f2c-483a-8818-ca932cb42636.png)

### 字段 
- Reference
    - next
        - 入队后，指向队列中的下一节点

            | 状态     | 数值      | 描述                      |
            |----------|-----------|---------------------------|
            | active   | null      | 状态变更后，由 GC 自动赋值 |
            | pending  | this      |                           |
            | enqueued | next/this | `入队后，指向下一节点`     |
            | inactive | this      |                           |


    - static pending
        - pending 状态队列首元素

    - discovered

        | 状态    | 数值                          |
        |---------|-------------------------------|
        | active  | discovered 引用列表的下一元素 |
        | pending | `pending 列表的下一元素`      |
        | other   | NULL                          |

- T
    - referent
        - 引用所指向的对象

- ReferenceQueue
    - queue
        - 引用队列
            - 仅持有首元素指针，队列封装于元素内部（指向下一节点）
            - 封装出入队的方法，操作元素指针等
        - 主要由 Reference-handler 线程调用
        - 通过元素的 discovered 字段来链接至下一元素

- Lock
    - lock
        - 引用操作的全局唯一锁


### 初始化

```java
static{
    // 获取最上层线程组
    ThreadGroup tg= Thread.currentThread().getThreadGroup().getParent()...
    new ReferenceHandler(tg, "Reference Handler")
        .setPriority(Thread.MAX_PRIORITY)       // 最高优先级
        .setDaemon(true)                        // 守护进程
        .start()
    // 全局共享类，设置
    // Bits.reserveMemory 中用于回收内存资源
    SharedSecrets.setJavaLangRefAccess({
        tryHandlePending(false)
    })
}
```

### 方法


- tryHandlePending
> 将 pending 中的元素入队

```java
// 将 pending 指向的引用弹出并添加至队列中
static boolean tryHandlePending(boolWaitForNotify){
    synchronized (lock) {
        if(null!= pending){             // reference.referent 被 GC 回收后同步至 pending 队列 
            r = pending;
            pending = r.discovered;     // 元素后移
            r.discovered = null;
        }else{
            lock.wait();    // OutOfMemoryError
        }
    }
    ((Cleaner)r).clean();              // 执行相应动作，如 Bits 中绑定的内存释放
    r.queue.enqueue(r);
}
```

## Lock    
> 普通对象，用于 GC 同步锁之用


## ReferenceHandler
> 处理被回收对象的引用  
> 将 pending 节点入队，并切换至 Reference.discovered 指向的下一节点

### 初始化

```java
static{
    // 提前加载类，避免后续懒加载时内存不足
    ensureClassInitialized(Cleaner.class);
}
```

### 方法
- run
```java
while(true){
    // 守护进程，检测 pending 对象是否指向有效节点
    tryHandlePending(true)
}
```

## ReferenceQueue
> 将已被回收的对象引用存入 Queue 中，以通知来进行额外的工作
>>如果引用列表为 NULL，则出入队为空操作（默认情况）  
>>元素入队后，将其 queue 更新为 ENQUEUED，以避免重复入队操作

### 属性
- ReferenceQueue<Object>
    - NULL            
        - 队列未指定情况，或已被移除元素
    - ENQUEUED        
        - 避免重复入队的判断，入队后便调整 queue 值为 ENQUEUED
        
- Lock lock
    - 队列内操作的唯一锁

- Reference volatile head
    - 指向队首元素

- Long  queueLength
    - 队列中元素的数量 


### 方法

- enqueue
> 入队

```java 
boolean enqueue(Reference r){
    synchronized (lock) {
        if(r.queue== NULL|ENQUEUED){
            return false;
        }
        r.queue= ENQUEUED;
        r.next= (null== head)? r: head;
        head= r;
        queueLength++;
    }
}
```

- poll
> 出队

```java 
Reference poll(){
    synchronized (lock) {
        head= r.next;
        r.queue= NULL;
        r.next= r;
        queueLength--;
    }
}
```

- remove
> 移除队首元素

```java 
// timeout= 0 时表示一直等待
Reference remove(long timeout){ 

    if(poll()!= null) return;
    for(;;){
        lock.wait(timeout);
        if(poll()!= null) return;
        timeout= System.nanoTime()- start;  // 统计计算时间，超过则返回 null
    }
}
```

## FinalReference
> 空实现，将作用域缩小至 Package 级别

## Finalizer
> 针对覆写 Object.finalize 方法的类的实例
>> 第一次 GC 时将实例更新至 unfinalized 队列中  
>> 等待出队处理完成后，移除出队列，待第二次 GC 再清除

### 属性
- Finalizer
    - static unfinalized
        - 待完成 finalized 方法调用的实例的队列

- T    
    - referenct
        - 由 JVM 在 GC 时进行赋值

- ReferenceQueue<? super T>        
    - queue


### 方法
- add
> 将节点添加至 unfinalized 队列中

```java
synchronized (lock) {
    if (unfinalized != null) {
        this.next = unfinalized;
        unfinalized.prev = this;
    }
    unfinalized = this;
}
```

- runFinalizer
> 调用当前使用的 finalize 方法

```java
synchronized (this) {
    if (hasBeenFinalized()) return;
    remove();
}
try {
    Object finalizee = this.get();
    if (finalizee != null && !(finalizee instanceof java.lang.Enum)) {
        // 运行实例的 finalize 方法
        jla.invokeFinalize(finalizee);
        finalizee = null;
    }
} catch (Throwable x) { }
super.clear();
```

### 内部类
- FinalizerThread
> 守护进程形式，静态启动
>> 获取队列中元素，调用 runFinalizer 方法

```java
private static class FinalizerThread extends Thread {
    public void run() {
        final JavaLangAccess jla = SharedSecrets.getJavaLangAccess();
        for (;;) {
            try {
                Finalizer f = (Finalizer)queue.remove();
                f.runFinalizer(jla);
            } catch (InterruptedException x) {}
        }
    }
}

static {
    ThreadGroup tg = Thread.currentThread().getThreadGroup();
    for (ThreadGroup tgn = tg;
            tgn != null;
            tg = tgn, tgn = tg.getParent());
    Thread finalizer = new FinalizerThread(tg);
    finalizer.setPriority(Thread.MAX_PRIORITY - 2);
    finalizer.setDaemon(true);
    finalizer.start();
}
```

## SoftReference
>

## WeakHashMap
> 自动移除不再使用的键值对

### 属性
- ReferenceQueue<Object>
    - queue

### 方法

- expungeStaleEntries
> 移除除旧事项
>> 使用场景： getTaable()/ size()/ resize(int)

```java
for (Object x; (x = queue.poll()) != null; ) {
    prev= p= table[x.hash]
    if(p== x){
        prev.next= p.next;
        size--;
        break;
    }
}
```

### 内部类
- Entry
    - `class Entry<K,V> extends WeakReference<Object> implements Map.Entry<K,V>`


## DirectByteBuffer
>


# 使用场景

| 类型   | 场景                                                                                                                 |
|------|--------------------------------------------------------------------------------------------------------------------|
| 强引用 | `Object o= new Object();`普遍场景<br>内存不足时，JVM 抛出 OOM 异常<br>`o= null` 或超出生命周期后回收                  |
| 软引用 | 内存足够蛙保存，内存不足时回收<br>缓存场景                                                                            |
| 弱引用 | 对象不被使用时，即被回收<br>WeakHashMap 场景                                                                          |
| 虚引用 | 不影响对象的生命周期<br>get()始终为 null<br>仅用于 GC 后收到系统通知，如堆外内存的管理<br>常结合 Cleaner 进行回收操作 |


# 补充
## 对象可达性
### 起点
- GC-Roots
    - 对象
        - 虚拟机`栈`所引用的对象
        - 方法区中`静态属性`和`常量`所引用的对象
        - 本地方法栈中 `JNI` 引用的对象
    - 图示
        - ![GRoot.png](http://doc.yqjdcyy.com/ea210e0a-cda5-4733-9859-e704e21f130b.png)

### 原则 
- 单一路径，以最弱引用为准
- 多路径，以最强引用为准

### 示例
- 关联

| 对象 | 关联            | 对象  | 关联            | 对象 |
|------|-----------------|-------|-----------------|------|
| Root | StrongReference | Obj-1 | SoftReference   | Obj  |
| -    | StrongReference | Obj-2 | StrongReference | -    |
| -    | StrongReference | Obj-3 | WeakReference   | -    |

- 结论
    - 三层关系中， Obj 对应的引用分别为 Soft/Strong/Weak（以最弱为准）
    - 而对比三者，则引用类型为 Soft（以最强为准）

## 对象存活判断
### 判断条件（两次标记）
- 可达性标记
    - 对象不可达时，一次标记
- `finalize()` 方法标记
    - 已由 JVM 调用或未覆盖情况下，不执行
    - 需执行，则该对象将入队至 `F-Queue`，等待低级别 `Finalizer` 线程进行读取/执行
    - 执行时，可通过建立引用，来避免被回收；否则将被标记并回收


# 参考
## SharedSecrets
- [使用 SharedSecrets 获取 JVM 实例](https://blog.csdn.net/yums467/article/details/53005292)
    - `java.util.logging.LogRecord#inferCaller()`
- []()
- []()

## Reference
- [JDK源码分析（7）之 Reference 框架概览](http://www.lumajia.com/htmls/1200985533134668802.html)
- [Java Reference详解](https://my.oschina.net/robinyao/blog/829983)
    - 针对 Finalizer 机制等有相关补充
- [深入理解JDK中的Reference原理和源码实现](https://www.throwable.club/2019/02/16/java-reference/#Reference%E7%9A%84%E7%8A%B6%E6%80%81%E9%9B%86%E5%90%88)
    - 状态环节描述详尽，配有流程转换图
    - 二次标记(finalize)
- [深入理解 java 中的 *Reference](https://blog.csdn.net/xlinsist/article/details/57089288)
    - 较为丰富的使用场景/示例
        - 如内存泄漏写法/WeakHashMap 等
- [Reference 、ReferenceQueue 详解](https://www.jianshu.com/p/f86d3a43eec5)
    - 针对 JVM 操作进行了相关补充