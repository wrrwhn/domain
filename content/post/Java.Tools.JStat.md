+++
date = "2018-05-03T23:20:00+08:00"
title = "Java.Tools.JStat"
draft = false
tags = ["整理","Java","Tools"]
share = true
+++

[TOC]

# JSTAT
## 作用
    - 监视vm内存内的各种**堆**和非堆的**大小**及其**内存使用**量，并可观察classloader，compiler，**gc**相关信息

## 调用
- `jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]`
- 参数
    - `option`
        - 选项
            - `–class` 
                - 监视**类装载**、**卸载数量**、**总空间**及类装载所**耗费时间** 
                    | 字段     | 描述                   | 数值    |
                    |----------|------------------------|---------|
                    | Loaded   | 装载类的数量           | 11736   |
                    | Bytes    | 装载类所占字节数       | 22293.3 |
                    | Unloaded | 卸载类的数量           | 0       |
                    | Bytes    | 卸载类所占字节数       | 0.0     |
                    | Time     | 装载和卸载类所花的时间 | 13.24   |
            - `–gc` 
                - 监视Java**堆状况**，包括Eden区、2个Survivor区、老年代、永久代等的容量
                    | 字段 | 描述                                                | 数值     |
                    |------|-----------------------------------------------------|----------|
                    | S0C  | 年轻代中第一个survivor（幸存区）的容量 (字节)         | 20480.0  |
                    | S1C  | 年轻代中第二个survivor（幸存区）的容量 (字节)         | 20480.0  |
                    | S0U  | 年轻代中第一个survivor（幸存区）目前已使用空间 (字节) | 8.0      |
                    | S1U  | 年轻代中第二个survivor（幸存区）目前已使用空间 (字节) | 0.0      |
                    | EC   | 年轻代中Eden的容量 (字节)                           | 163840.0 |
                    | EU   | 年轻代中Eden目前已使用空间 (字节)                   | 35825.1  |
                    | OC   | Old代的容量 (字节)                                  | 409600.0 |
                    | OU   | Old代目前已使用空间 (字节)                          | 132443.4 |
                    | PC   | Perm(持久代)的容量 (字节)                           | �        |
                    | PU   | Perm(持久代)目前已使用空间 (字节)                   | �        |
                    | YGC  | 从应用程序启动到采样时年轻代中gc次数                | 172      |
                    | YGCT | 从应用程序启动到采样时年轻代中gc所用时间(s)         | 2.254    |
                    | FGC  | 从应用程序启动到采样时old代(全gc)gc次数             | 4        |
                    | FGCT | 从应用程序启动到采样时old代(全gc)gc所用时间(s)      | 0.180    |
                    | GCT  | 从应用程序启动到采样时gc用的总时间(s)               | 2.434    |                
            - `–gccapacity` 
                - 监视内容与`-gc`基本相同，但输出主要关注Java堆各个区域使用到的**最大和最小空间**
                    | 字段  | 描述                                          | 数值     |
                    |:------|:----------------------------------------------|:---------|
                    | NGCMN | 年轻代(young)中初始化(最小)的大小(字节)       | 204800.0 |
                    | NGCMX | 年轻代(young)的最大容量 (字节)                | 204800.0 |
                    | NGC   | 年轻代(young)中当前的容量 (字节)              | 204800.0 |
                    | S0C   | 年轻代中第一个survivor（幸存区）的容量 (字节) | 20480.0  |
                    | S1C   | 年轻代中第二个survivor（幸存区）的容量 (字节) | 20480.0  |
                    | EC    | 年轻代中Eden（伊甸园）的容量 (字节)           | 163840.0 |
                    | OGCMN | old代中初始化(最小)的大小 (字节)              | 409600.0 |
                    | OGCMX | old代的最大容量(字节)                         | 409600.0 |
                    | OGC   | old代当前新生成的容量 (字节)                  | 409600.0 |
                    | OC    | Old代的容量 (字节)                            | 409600.0 |
                    | PGCMN | perm代中初始化(最小)的大小 (字节)             | �        |
                    | PGCMX | perm代的最大容量 (字节)                       | �        |
                    | PGC   | perm代当前新生成的容量 (字节)                 | �        |
                    | PC    | Perm(持久代)的容量 (字节)                     | �        |
                    | YGC   | 从应用程序启动到采样时年轻代中gc次数          | 173      |
                    | FGC   | 从应用程序启动到采样时old代(全gc)gc次数       | 4        |
            - `–gcutil`
                - 监视内容与`-gc`基本相同，但输出主要关注已使用空间**占总空间**的百分**比**
                    | 字段 |                           描述                           |  数值 |
                    |------|----------------------------------------------------------|-------|
                    | S0   | 年轻代中第一个survivor（幸存区）已使用的占当前容量百分比 | 0.00  |
                    | S1   | 年轻代中第二个survivor（幸存区）已使用的占当前容量百分比 | 0.04  |
                    | E    | 年轻代中Eden（伊甸园）已使用的占当前容量百分比           | 6.00  |
                    | O    | old代已使用的占当前容量百分比                            | 32.33 |
                    | P    | perm代已使用的占当前容量百分比                           | �     |
                    | YGC  | 从应用程序启动到采样时年轻代中gc次数                     | 173   |
                    | YGCT | 从应用程序启动到采样时年轻代中gc所用时间(s)              | 2.258 |
                    | FGC  | 从应用程序启动到采样时old代(全gc)gc次数                  | 4     |
                    | FGCT | 从应用程序启动到采样时old代(全gc)gc所用时间(s)           | 0.180 |
                    | GCT  | 从应用程序启动到采样时gc用的总时间(s)                    | 2.438 |                    
            - `–gccause` 
                - 与`-gcutil`功能一样，但是会额外输出导致**上一次GC产生的原因**
            - `–gcnew` 
                - 监视**新生代GC**的状况
                    | 字段 |                          描述                         |    数值   |
                    |------|-------------------------------------------------------|-----------|
                    | S0C  | 年轻代中第一个survivor（幸存区）的容量 (字节)         | 20480.0   |
                    | S1C  | 年轻代中第二个survivor（幸存区）的容量 (字节)         | 20480.0   |
                    | S0U  | 年轻代中第一个survivor（幸存区）目前已使用空间 (字节) | 0.0       |
                    | S1U  | 年轻代中第二个survivor（幸存区）目前已使用空间 (字节) | 8.0       |
                    | TT   | 持有次数限制                                          | 6         |
                    | MTT  | 最大持有次数限制                                      | 6 10240.0 |
                    | EC   | 年轻代中Eden（伊甸园）的容量 (字节)                   | 163840.0  |
                    | EU   | 年轻代中Eden（伊甸园）目前已使用空间 (字节)           | 13029.7   |
                    | YGC  | 从应用程序启动到采样时年轻代中gc次数                  | 173       |
                    | YGCT | 从应用程序启动到采样时年轻代中gc所用时间(s)           | 2.258     |                    
            - `–gcnewcapacity` 
                - 监视内容与`-gcnew`基本相同，输出主要关注使用到的**最大和最小空间** 
                    |  字段 |                        描述                       |   数值   |
                    |-------|---------------------------------------------------|----------|
                    | NGCMN | 年轻代(young)中初始化(最小)的大小(字节)           | 204800.0 |
                    | NGCMX | 年轻代(young)的最大容量 (字节)                    | 204800.0 |
                    | NGC   | 年轻代(young)中当前的容量 (字节)                  | 204800.0 |
                    | S0CMX | 年轻代中第一个survivor（幸存区）的最大容量 (字节) |  20480.0 |
                    | S0C   | 年轻代中第一个survivor（幸存区）的容量 (字节)     |  20480.0 |
                    | S1CMX | 年轻代中第二个survivor（幸存区）的最大容量 (字节) |  20480.0 |
                    | S1C   | 年轻代中第二个survivor（幸存区）的容量 (字节)     |  20480.0 |
                    | ECMX  | 年轻代中Eden（伊甸园）的最大容量 (字节)           | 163840.0 |
                    | EC    | 年轻代中Eden（伊甸园）的容量 (字节)               | 163840.0 |
                    | YGC   | 从应用程序启动到采样时年轻代中gc次数              |      173 |
                    | FGC   | 从应用程序启动到采样时old代(全gc)gc次数           |        4 |                    
            - `–gcold` 
                - 监视**老年代GC**的状况
                    | 字段 |                      描述                      |   数值   |
                    |------|------------------------------------------------|----------|
                    | PC   | Perm(持久代)的容量 (字节)                      | �        |
                    | PU   | Perm(持久代)目前已使用空间 (字节)              | �        |
                    | OC   | Old代的容量 (字节)                             | 409600.0 |
                    | OU   | Old代目前已使用空间 (字节)                     | 132443.4 |
                    | YGC  | 从应用程序启动到采样时年轻代中gc次数           | 173      |
                    | FGC  | 从应用程序启动到采样时old代(全gc)gc次数        | 4        |
                    | FGCT | 从应用程序启动到采样时old代(全gc)gc所用时间(s) | 0.180    |
                    | GCT  | 从应用程序启动到采样时gc用的总时间(s)          | 2.438    |                    
            - `–gcoldcapacity` 
                - 监视内容与`—gcold`基本相同，输出主要关注使用到的**最大和最小空间**
                    |  字段 |                      描述                      |   数值   |
                    |-------|------------------------------------------------|----------|
                    | OGCMN | old代中初始化(最小)的大小 (字节)               | 409600.0 |
                    | OGCMX | old代的最大容量(字节)                          | 409600.0 |
                    | OGC   | old代当前新生成的容量 (字节)                   | 409600.0 |
                    | OC    | Old代的容量 (字节)                             | 409600.0 |
                    | YGC   | 从应用程序启动到采样时年轻代中gc次数           |      173 |
                    | FGC   | 从应用程序启动到采样时old代(全gc)gc次数        |        4 |
                    | FGCT  | 从应用程序启动到采样时old代(全gc)gc所用时间(s) |    0.180 |
                    | GCT   | 从应用程序启动到采样时gc用的总时间(s)          |    2.438 |                    
            - `–gcpermcapacity` 
                - 输出**永久代**使用到的**最大和最小空间**
                    |  字段 |                      描述                      |  数值 |
                    |-------|------------------------------------------------|-------|
                    | PGCMN | perm代中初始化(最小)的大小 (字节)              | �     |
                    | PGCMX | perm代的最大容量 (字节)                        | �     |
                    | PGC   | perm代当前新生成的容量 (字节)                  | �     |
                    | PC    | Perm(持久代)的容量 (字节)                      | �     |
                    | YGC   | 从应用程序启动到采样时年轻代中gc次数           | 173   |
                    | FGC   | 从应用程序启动到采样时old代(全gc)gc次数        | 4     |
                    | FGCT  | 从应用程序启动到采样时old代(全gc)gc所用时间(s) | 0.180 |
                    | GCT   | 从应用程序启动到采样时gc用的总时间(s)          | 2.438 |                    
            - `–compiler` 
                - 输出**JIT编译**器编译过的**方法、耗时**等信息
                    | 字段         | 描述                               | 数值                                                |
                    |:-------------|:-----------------------------------|:----------------------------------------------------|
                    | Compiled     | 编译任务执行数量                   | 9726                                                |
                    | Failed       | 编译任务执行失败数量               | 3                                                   |
                    | Invalid      | 编译任务执行失效数量               | 0                                                   |
                    | Time         | 编译任务消耗时间                   | 85.85                                               |
                    | FailedType   | 最后一个编译失败任务的类型         | 1                                                   |
                    | FailedMethod | 最后一个编译失败任务所在的类及方法 | com/mysql/jdbc/AbandonedConnectionCleanupThread run |
            - `–printcompilation` 
                - 输出已经**被JIT编译的方法**
                - 可由 `-XX:+PrintComplation` 设置
                    |   字段   |              描述              |                              数值                             |
                    |----------|--------------------------------|---------------------------------------------------------------|
                    | Compiled | 编译任务的数目                 | 9726                                                          |
                    | Size     | 方法生成的字节码的大小         | 582                                                           |
                    | Type     | 编译类型                       | 1                                                             |
                    | Method   | 类名和方法名用来标识编译的方法 | org/springframework/data/redis/core/RedisTemplate$6 doInRedis |                    
    - `vmid`
        - VM 进程号，即当前运行 java 进程号
            - VMID= Vitrual Machine IDentifier
            - LVMID= Local Vitrual Machine IDentifier
        - 情况
            - 本地虚拟机进程
                - VMID= LVMID
            - 远程虚拟机进程
                = VMID= `[protocol:][//] lvmid [@hostname[:port]/servername]`
    - `interval`
        - 间隔时间，单位为秒或毫秒
    - `count`
        - 打印次数，默认不限制


## 示例
- `/usr/java/jdk1.7.0_60/bin/jstat -class 29003`
    ```
    Loaded  Bytes   Unloaded    Bytes     Time   
    11736   22293.3        0     0.0      13.24
    ```
- `/usr/java/jdk1.7.0_60/bin/jstat -gc 29003`
    ```
    Warning: Unresolved Symbol: sun.gc.generation.2.space.0.capacity substituted NaN
    Warning: Unresolved Symbol: sun.gc.generation.2.space.0.used substituted NaN
    S0C    S1C    S0U    S1U      EC       EU        OC         OU       PC     PU    YGC     YGCT    FGC    FGCT     GCT   
    20480.0 20480.0  8.0    0.0   163840.0 35825.1   409600.0   132443.4    �      �       172    2.254   4      0.180    2.434
    ```
- `/usr/java/jdk1.7.0_60/bin/jstat -compiler 29003`
    ```
    Compiled Failed Invalid   Time   FailedType FailedMethod
        9726      3       0    85.85          1 com/mysql/jdbc/AbandonedConnectionCleanupThread run
    [appuser@iZ94nvigjtdZ ~]$ /usr/java/jdk1.7.0_60/bin/jstat -gccapacity 29003
    Warning: Unresolved Symbol: sun.gc.generation.2.minCapacity substituted NaN
    Warning: Unresolved Symbol: sun.gc.generation.2.maxCapacity substituted NaN
    Warning: Unresolved Symbol: sun.gc.generation.2.capacity substituted NaN
    Warning: Unresolved Symbol: sun.gc.generation.2.space.0.capacity substituted NaN
    NGCMN    NGCMX     NGC     S0C   S1C       EC      OGCMN      OGCMX       OGC         OC      PGCMN    PGCMX     PGC       PC     YGC    FGC 
    204800.0 204800.0 204800.0 20480.0 20480.0 163840.0   409600.0   409600.0   409600.0   409600.0        �        �        �        �    173     4
    ```
- `/usr/java/jdk1.7.0_60/bin/jstat -gcnew 29003`
    ```
     S0C    S1C    S0U    S1U   TT MTT  DSS      EC       EU     YGC     YGCT  
    20480.0 20480.0    0.0    8.0  6   6 10240.0 163840.0   9772.3    173    2.258      
    ```
- `/usr/java/jdk1.7.0_60/bin/jstat -gcutil 29003`
    ```
    Warning: Unresolved Symbol: sun.gc.generation.2.space.0.capacity substituted NaN
    Warning: Unresolved Symbol: sun.gc.generation.2.space.0.used substituted NaN
    Warning: Unresolved Symbol: sun.gc.generation.2.space.0.capacity substituted NaN
    S0     S1     E      O      P     YGC     YGCT    FGC    FGCT     GCT   
    0.00   0.04   6.00  32.33      �    173    2.258     4    0.180    2.438
    ```
- `/usr/java/jdk1.7.0_60/bin/jstat -gcnew 29003`
    ```
     S0C    S1C    S0U    S1U   TT MTT  DSS      EC       EU     YGC     YGCT  
    20480.0 20480.0    0.0    8.0  6   6 10240.0 163840.0  13029.7    173    2.258
    ```
- `/usr/java/jdk1.7.0_60/bin/jstat -gcnewcapacity 29003`
    ```
    NGCMN      NGCMX       NGC      S0CMX     S0C     S1CMX     S1C       ECMX        EC      YGC   FGC 
    204800.0   204800.0   204800.0  20480.0  20480.0  20480.0  20480.0   163840.0   163840.0   173     4
    ```
- `/usr/java/jdk1.7.0_60/bin/jstat -gcold 29003`
    ```
    Warning: Unresolved Symbol: sun.gc.generation.2.space.0.capacity substituted NaN
    Warning: Unresolved Symbol: sun.gc.generation.2.space.0.used substituted NaN
    PC       PU        OC          OU       YGC    FGC    FGCT     GCT   
        �        �    409600.0    132443.4    173     4    0.180    2.438
    ```
- `/usr/java/jdk1.7.0_60/bin/jstat -gcoldcapacity 29003`
    ```
    OGCMN       OGCMX        OGC         OC       YGC   FGC    FGCT     GCT   
    409600.0    409600.0    409600.0    409600.0   173     4    0.180    2.438
    ```
- `jstat -gcutil 12191 250 7`
    - 统计 GC 时 heap 情况，间隔250毫秒，打印7次



# Reference
- [Java命令学习系列（四）——jstat](http://www.hollischuang.com/archives/481)
- [jstat](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jstat.html)