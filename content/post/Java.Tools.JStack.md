---
title: "Java.Tools.JStack"
date: "2018-05-03"
categories:
 - "整理"
tags:
 - "Java"
 - "tools"
toc: true
---


# JSTACK
## 作用
- 观察当前java虚拟机内每一条线程正在执行的**方法堆栈**的集合，以定位线程出现长时间停顿的原因，如线程间死锁、死循环、请求外部资源导致的长时间等待等
- `java` 程序崩溃生成 `core` 文件，`jstack`工具可以用来获得 `core` 文件的`java stack`和n`ative stack`的信息，从而可以轻松地知道 `java` 程序是如何崩溃和在程序何处发生问题
- Java **1.8** 版本中**不支持**

## 机制
- 生成java虚拟机当前时刻的线程快照
- 观察 Object.Monitor 于线程拥有和区域的情况，获得各进程的相关情况
    |    区域   |                     状态                     |                                            描述                                            |
    |-----------|----------------------------------------------|--------------------------------------------------------------------------------------------|
    | Entry Set | Waiting Thread<br/>Waiting for monitor entry | BLOCKED<br/>waiting for monitor entry<br/>线程进入临界区（ synchronized 保护起来的代码区） |
    | The Owner | Active Thread                                | RUNNABLE                                                                                   |
    | Wait Set  | Waiting Thread<br/>in Object.wait()          | WAITING / TIMED_WAITING <br/> in Object.wait()/ waiting on condition                       |
- ![thread.bmp](http://otzm88f21.bkt.clouddn.com/e320bd53-2e3e-4387-ba72-18711061a224.bmp)

## 调用
- `jstack [-Fml|h] <pid>`
- 参数
    - `-F`
        - `jstack <pid>`未响应或进程挂起时，**强制 `Dump` **出线程数据
    - `-m`
        - 混合模式打印出 `java` 和本机的框架信息
    - `-l`
        - 长清单，显示锁相关信息
    - `-h`
        - 打印出帮助文档信息
- 状态
    - `NEW`
        - 未启动的不会出现在 `Dump` 中
    - `RUNNABLE`
        - 在虚拟机内执行的
    - `BLOCKED`
        - 受阻塞并等待监视器锁
    - `WATING`
        - 无限期等待另一个线程执行特定操作
    - `TIMED_WATING`
        - 有时限的等待另一个线程的特定操作
    - `TERMINATED`
        - 已退出的
- 修饰
    - `locked <地址> 目标`
        - 使用 `synchronized` 申请对象锁成功,监视器的拥有者。
    - `waiting to lock <地址> 目标`
        - 使用 `synchronized` 申请对象锁未成功,在进入区等待。
    - `waiting on <地址> 目标`
        - 使用 `synchronized` 申请对象锁成功后,释放锁幵在等待区等待。
    - `parking to wait for <地址> 目标`
- 动作
    - `runnable`
        - 状态一般为 `RUNNABLE`
    - `in Object.wait()`
        - 等待区等待,状态为 `WAITING` 或 `TIMED_WAITING`
    - `waiting for monitor entry`
        - 进入区等待,状态为 `BLOCKED`
    - `waiting on condition`
        - 等待区等待、被 `park`
    - `sleeping`
        - 休眠的线程,调用了 `Thread.sleep()`

## 示例
- `/usr/java/jdk1.7.0_60/bin/jstack 29003`
    ```
    "http-nio-127.0.0.1-29029-ClientPoller-0" #28 daemon prio=5 os_prio=0 tid=0x00007f9d8c4a5800 nid=0x71ba runnable [0x00007f9d513be000]
    java.lang.Thread.State: RUNNABLE

    "ContainerBackgroundProcessor[StandardEngine[Catalina]]" #27 daemon prio=5 os_prio=0 tid=0x00007f9d8c4a4000 nid=0x71b9 waitin on condition [0x00007f9d514bf000]
    java.lang.Thread.State: TIMED_WAITING (sleeping)

    "Abandoned connection cleanup thread" #24 daemon prio=5 os_prio=0 tid=0x00007f9d15de4000 nid=0x7188 in Object.wait() [0x00007f9d138fd000]
    java.lang.Thread.State: TIMED_WAITING (on object monitor)
    ```
- `while(true)`
    ```
    "main" #1 prio=5 os_prio=0 tid=0x0000000005422800 nid=0x20e0 runnable [0x000000000517f000]
    java.lang.Thread.State: RUNNABLE
            at com.yao.common.thread.ThreadBlockTest.main(ThreadBlockTest.java:12)    
    ```
- `lock`
    - [jstack-deadlock.log](http://otzm88f21.bkt.clouddn.com/f01c473b-eabd-4337-a0e0-3d8d3d571494.log)
    ```
    Found one Java-level deadlock:
    =============================
    "Thread-1":
    waiting to lock monitor 0x0000000004b47828 (object 0x000000076b45c4d0, a java.lang.Object),
    which is held by "Thread-0"
    "Thread-0":
    waiting to lock monitor 0x00000000209a0a68 (object 0x000000076b45c4e0, a java.lang.Object),
    which is held by "Thread-1"

    Java stack information for the threads listed above:
    ===================================================
    "Thread-1":
            at com.yao.common.thread.DeadLock.run(ThreadLockDead.java:52)
            - waiting to lock <0x000000076b45c4d0> (a java.lang.Object)
            - locked <0x000000076b45c4e0> (a java.lang.Object)
            at java.lang.Thread.run(Thread.java:745)
    "Thread-0":
            at com.yao.common.thread.DeadLock.run(ThreadLockDead.java:39)
            - waiting to lock <0x000000076b45c4e0> (a java.lang.Object)
            - locked <0x000000076b45c4d0> (a java.lang.Object)
            at java.lang.Thread.run(Thread.java:745)    
    ```



# Reference
- 官方
	- [jstack](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jstack.html)    
	- [2.16 The jstack Utility](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/tooldescr016.html)
- 整理
	- [Java命令学习系列（二）——Jstack](http://www.hollischuang.com/archives/110)
- 补充
    - [如何使用jstack分析线程状态](http://www.importnew.com/23601.html)
    - [java死锁的例子](https://blog.csdn.net/neu_yousei/article/details/23827631)