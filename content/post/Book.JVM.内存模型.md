---
title: "Book.JVM.内存模型"
date: "2020-05-05"
categories:
 - "阅读"
tags:
 - "Java"
 - "JVM"
toc: true
---



## 代码示例

```java
@Slf4j
public class VolatileTest {

    @Test
    public void testNoVolatile() {

        final NoVolatileThread thread = new NoVolatileThread();
        thread.start();
        while (true) {
            if (thread.isStatus()) {
                log.info("NoVolatileThread.status change to TRUE");
                return;
            }
        }
    }
}

@Slf4j
class NoVolatileThread extends Thread {

    @Getter
    private boolean status = false;

    @Override
    public void run() {

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        status = true;
        log.info("status change to TRUE");
    }
}
```

- 请问上述代码的执行结果是如何？为什么这样？

```
23:16:07.361 [Thread-0] INFO com.yao.java.keyword.NoVolatileThread - status change to TRUE
```

## 为什么不更新

- 根本原因为 VolatileTest 线程获取 NoVolatileThread 实例的数据时，早于 NoVolatileThread 所属线程变更其实例属性之前
- 导致 VolatileTest 线程的缓存中 NoVolatileThread 实例的属性值一直为 false 且无法更新
- 而更多实现细节，就需要了解一下 Java 的内存模型（Java Memory Model）

## 线程和内存的关系

- 物理结构
    - 由于 CPU 执行指令的速度远超内存的读写，通过增加高速缓存（工作内存）来保障处理器的运行
    - ![JMM-物理结构.png](http://doc.yqjdcyy.com/c1ece5c0-5977-447d-ae0f-5d4d3fa795fd.png)

- 抽象逻辑
    - 为了兼容不同物理硬件，不同语言，进行了逻辑上的内存模型定义
    - 其中主存中存放对象实例的数据部分，而工作内存类比于高速缓存，存储虚拟机栈中的部分局部数据
    - 特别注意的是，拷贝副本时，不会完整拷贝对象，仅拷贝计算所需要的字段
    - ![JMM-逻辑结构.png](http://doc.yqjdcyy.com/db96a990-d0af-4999-accf-69f8906133ff.png)

- 特性
    - 原子性
        - read、load、assign、use、store和 write（扩展中内存的交互动作）操作均具备原子性
    - 可见性
        - 变量修改后，将新值同步回主存；在变量读取前从主内存刷新变量值的方式来实现
        - volatile、synchronized 和 final 关键字均支持
            - volatitle 通过将其它工作内存的对象置为无效
            - synchronized 则是执行 unlock 前必须将变量同步回主存
            - final 修饰的字段初始化完成后，其它线程就能查看该值（读写均在初始化之后）
    - 有序性
        - 观察自身线程时，`线程内表现为串行的语义`
            - `As-If-Serial`
        - 其它线程观察本线程时，所有操作是无序的
            - 指令重排序+ 工作内存与主存同步延迟

## 解决方法

- 使用 volatile 修饰 NoVolatileThread.status
    - 通知其它 CPU 该缓存值已失效，重新至主存拉取
- testNoVolatile 中于调用前使用 synchronized 锁定 NoVolatileThread
    - 获得和释放锁前后，强制进行主内存和工作内存间对象值的同步

## 什么是 volatile

- 虚拟机提供的最轻量级的同步机制
- 特性
    - 原子性
        - 同 JMM 
    - 可见性
        - 不同线程获取共享变量， volatile 所修饰的对象被修改并写回主存时，其它线程将立即查看到最新值
    - 有序性
        - 禁止指令重排序优化

- 适合场景（不依赖变量值进行运算 | 不需与其它变量一同作为条件判断 
    - 状态位
        - 多线程的逻辑状态判断位
    - 双重检查
        - 单例模式
    - 低开销读写锁

- 注意事项
    - Java 运算非原子操作情况下，会导致 volatile 变量的运算在并发下不安全
        - 如 `i++` 的实际执行指令为 `getstatic/ iconst_1/ iadd/ putstatic`

## 扩展
### MESI 等缓存一致性协议如何实现
- CPU 写数据时，若发现其为共享变量（其它 CPU 中存在该变量的副本），则将发信号通知其它 CPU 该变量缓存已失效
- CPU 在读取变量缓存时，发现其状态已失效，将从主存重新加载

### MESI 如何通知其它 CPU
- CPU 通过`嗅探`总线（主存）上的数据，将对应的自己工作内存上缓存的对象（通过内存地址比对）的状态更新为失效。
- 注意 volatile 的使用场景，避免频繁操作导致的总线风暴

### 乱序执行
- 处理器对输入代码进行乱序执行优化，并在乱序执行后将结果重组，确保执行结果与顺序执行一致
- ![重排序.png](http://doc.yqjdcyy.com/323ff3ab-24ba-414c-bb97-337c42a75f3f.png)

### 内存交互
- 支持的原子操作类型
    - ![内存交互指令.png](http://doc.yqjdcyy.com/9027ebed-9a03-4e14-87c4-851594d1815a.png)

- 乱序执行规则
    - read+ load/ store+ write 不可单独出现
    - 不允许线程丢弃其最近的 assign 操作
    - 无 assign 操作情况下，不允许将数据由工作内存同步回主存
    - 变量来源于主存
    - 仅允许一个线程对同一变量进行 lcok 操作，unlock 需与 lock 数量一致
    - unlock 操作之前，需将变量同步回主存


### volatile 如何禁止指令重排序 
- 插入内存屏障以避免重排序对计算结果的影响
    - 遵循 `happens-before`
- ![内存屏障](http://doc.yqjdcyy.com/17e1f29a-9ceb-4fba-9ac4-2808dca57af2.png)


## 参考
- 《深入理解 Java 虚拟机》
    - 第 12 单 Java 内存模型与线程
- [Java 并发基础之内存模型](https://javadoop.com/post/java-memory-model)
- [阿里面试官没想到，一个Volatile我能扯半小时](https://www.bilibili.com/read/cv5830311)
    - 书本知识上，进行了部分扩展
- [Java线程内存模型,线程、工作内存、主内存](https://zhuanlan.zhihu.com/p/25474331)
- [java 同步的三种方式：volatile、锁、final](https://www.jianshu.com/p/4587fe83ae3c)