---
title: "Java.Tools.JMap"
date: "2018-05-04"
categories:
 - "整理"
tags:
 - "Java"
 - "tools"
toc: true
---


# JMAP
## 作用
- 为进程、核心文件或远程调试服务器打印**共享对象内存**映射或**堆内存**细节
- Java **1.8** 版本中**不支持**
    
## 调用

- `jmap [option] <pid>|<executable <core>> | <[server_id@]<remoteServerIP | hostname>>`
- 参数
    - `<no option>`
        - 如果使用不带选项参数的jmap打印共享对象映射，将会打印目标虚拟机中加载的每个共享对象的起始地址、映射大小以及共享对象文件的路径全称
    - `-dump:[live,]format=b,file=<filename>`
        - 以 `hprof` **二进制**格式转储 `Java` **堆**到指定**文件**中。live子选项是可选的
        - live子选项决定堆中只有活动的对象会被转储
        - 可用 `jhat` 浏览 `heap dump`
    - `-finalizerinfo`
        - 打印**等待终结**的**对象**信息
    - `-heap`
        - 打印一个堆的摘要信息，包括使用的GC算法、堆配置信息和generation wise heap usage
    - `-histo[:live]`
        - 打印堆的柱状图。其中包括每个Java类、对象数量、内存大小(单位：字节)- 、完全限定的类名
        - 打印的虚拟机内部的类名称将会带有一个’*’前缀。如果指定了live子选项，则只计算活动的对象
    - `-permstat`
        - Java **1.8** 版本中**不支持**
        - 打印Java堆内存的永久保存区域的类加载器的智能统计信息
        - 对于每个类加载器而言，它的名称、活跃度、地址、父类加载器、它所加载的类的数量和大小都会被打印
            - 包含的字符串数量和大小
    - `-F`
        - 强制模式。如果指定的pid没有响应，请使用jmap -dump或jmap -histo选项。此模式下，不支持live子选项
    - `-h|help`
        - 打印帮助信息
    - `-J<flag>`
        - 指定传递给运行jmap的JVM的参数

## 示例
- `jmap 24748`

    |      内存地址      | 内存占用 |          进程、核心文件或远程调试服务器           |
    |--------------------|----------|---------------------------------------------------|
    | 0x0000000051560000 | 8740K    | D:\server\Java\jdk1.8.0_65\jre\bin\server\jvm.dll |
    | 0x0000000054130000 | 104K     | D:\server\java\jdk1.8.0_65\jre\bin\net.dll        |

- `jmap -histo 24748`

    | 序号  | 实例数     | 所在字节数 | 类名                                                          |
    |-------|------------|------------|---------------------------------------------------------------|
    | num   | #instances | #bytes     | class name                                                    |
    | 14:   | 6778       | 108448     | java.lang.Object                                              |
    | 1565: | 1          | 16         | sun.util.resources.LocaleData$LocaleDataResourceBundleControl |
    | Total | 266178     | 50804480   |                                                               |

- `jmap -finalizerinfo 24748`
    ```
    Attaching to process ID 24748, please wait...
    Debugger attached successfully.
    Server compiler detected.
    JVM version is 25.65-b01
    Number of objects pending for finalization: 0
    ```
- `jmap -dump:format=b,file=d:/24748-jmap.dump 24748`
    ```
    Dumping heap to D:\24748-jmap.dump ...
    Heap dump file created
    ```
- `jmap -heap 24748`
    ```
    Attaching to process ID 24748, please wait...
    Debugger attached successfully.
    Server compiler detected.
    JVM version is 25.65-b01

    using thread-local object allocation.
    Parallel GC with 8 thread(s)              // GC 方式

    Heap Configuration:
    MinHeapFreeRatio         = 0                       // JVM堆最小空闲比例，默认`40`，可通过 `-XX:MinHeapFreeRatio` 设置
    MaxHeapFreeRatio         = 100                     // JVM堆最大空闲比例，默认`70`，可通过 `-XX:MaxHeapFreeRatio` 设置
    MaxHeapSize              = 734003200 (700.0MB)     // JVM堆的最大大小，可通过 `-XX:MaxHeapSize` 设置
    NewSize                  = 89128960 (85.0MB)       // JVM堆的 `新生代` 的默认大小，可通过 `-XX:NewSize` 设置
    MaxNewSize               = 244318208 (233.0MB)     // JVM堆的 `新生代` 的最大大小，可通过 `-XX:MaxNewSize=` 设置
    OldSize                  = 179306496 (171.0MB)     // JVM堆的 `老生代` 的大小，可通过 `-XX:OldSize` 设置
    NewRatio                 = 2                       // `新生代` 和 `老生代` 的大小比例，可通过 `-XX:NewRatio` 设置
    SurvivorRatio            = 8                       // 年轻代中 `Eden` 与 `Survivor` 的大小比例，可通过 `-XX:SurvivorRatio` 设置
    MetaspaceSize            = 21807104 (20.796875MB)  // JVM堆的 `元数据` 的大小，默认为`20.8M`，可通过 `-XX:MetaspaceSize` 设置
    CompressedClassSpaceSize = 1073741824 (1024.0MB)   // JVM堆的 `Klass Metaspace` 的大小，默认为`1G`，可通过 `-XX:CompressedClassSpaceSize` 设置
    MaxMetaspaceSize         = 17592186044415 MB       // JVM堆的 `元数据` 的最大大小，默认为`无穷大`，可通过 `-XX:MaxMetaspaceSize` 设置
    G1HeapRegionSize         = 0 (0.0MB)               // 设置 `Garbage First` 平分java堆产生的区域的大小，默认为`2的幂`，范围为 `1M至 32M`，可通过 `-XX:G1HeapRegionSize` 设置

    Heap Usage:             // 堆内存使用情况
    PS Young Generation
    Eden Space:             // Eden 区内存分布
    capacity = 67108864 (64.0MB)
    used     = 44377568 (42.321746826171875MB)
    free     = 22731296 (21.678253173828125MB)
    66.12772941589355% used
    From Space:             // Survivor 0 区内存分布
    capacity = 11010048 (10.5MB)
    used     = 6410528 (6.113555908203125MB)
    free     = 4599520 (4.386444091796875MB)
    58.224341982886905% used
    To Space:               // Survivor 1 区内存分布
    capacity = 11010048 (10.5MB)
    used     = 0 (0.0MB)
    free     = 11010048 (10.5MB)
    0.0% used
    PS Old Generation       // 老生代内存分布
    capacity = 179306496 (171.0MB)
    used     = 16384 (0.015625MB)
    free     = 179290112 (170.984375MB)
    0.009137426900584795% used

    5576 interned Strings occupying 475000 bytes.
    ```

# Reference
- 官方
	- [jmap](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jmap.html)   
	- [2.14 The jmap Utility](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/tooldescr014.html)
- 整理
	- [Java命令学习系列（三）——Jmap](http://www.hollischuang.com/archives/303)
- 补充
	- [MetaspaceSize的坑](http://atbug.com/java8-metaspace-size-issue/)
	- [JVM源码分析之Metaspace解密](http://lovestblog.cn/blog/2016/10/29/metaspace/)
	- [G1(Garbage First)的使用](http://bboniao.com/jvm/2014-03/g1garbage-first.html)
	- [垃圾优先型垃圾回收器调优](http://www.oracle.com/technetwork/cn/articles/java/g1gc-1984535-zhs.html)
	- [垃圾优先垃圾回收器(G1 GC)使用笔记](https://buzheng.org/2015/g1-gc-notes.html)