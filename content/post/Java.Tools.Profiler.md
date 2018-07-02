---
title: "Java.Tools.Profiler"
date: "2018-05-04"
categories:
 - "整理"
tags:
 - "Java"
 - "tools"
toc: true
---


# 监控
## 目的
- 通过收集程序运行时的信息来研究程序行为的动态分析方法。其目的在于**定位**程序需要被**优化**的部分，从而提高程序的运行速度或是内存使用效率

## 方式
- **事件**方法
    - 对于 Java，可以采用 **JVMTI（JVM Tools Interface）**API 来捕捉诸如方法调用、类载入、类卸载、进入/离开线程等事件，然后基于这些事件进行程序行为的分析。
- **统计抽样**方法（sampling）
    - 该方法每隔一段时间调用**系统中断**，然后收集当前的调用栈（call stack）信息，记录调用栈中出现的函数及这些函数的调用结构,基于这些信息得到函数的调用关系图及每个函数的 CPU 使用信息。 
    - 由于调用栈的信息是每隔一段时间来获取的，因此不是非常精确的，但由于该方法对目标程序的干涉比较少，目标程序的**运行速度**几乎不受影响。
- **植入附加指令**方法（BCI）
    - 该方法在目标程序中插入指令代码，这些指令代码将记录 profiling 所需的信息，包括运行时间、计数器的值等，从而给出一个较为精确的内存使用情况、函数调用关系及函数的 CPU 使用信息。
    - 该方法对程序执行速度会有一定的影响，因此给出的程序执行时间有可能不准确。但是该方法在统计程序的**运行轨迹**方面有一定的优势。

## 常用功能
- **遥测**（Telemetry）
    - 遥测是一种用来查看应用程序运行行为的最简单的方法。
    - 通常会有**多个视图**（View）分别实时地显示 CPU 使用情况、内存使用情况、线程状态以及其他一些有用的信息，以便用户能很快地发现问题的关键所在
    - CPU Telemetry 视图一般用于显示整个应用程序的 CPU 使用情况，有些工具还能显示应用程序中每个线程的 CPU 使用情况
    - Memory Telemetry 视图一般用于显示堆内存和非堆内存的分配和使用情况
    - Garbage Collection Telemetry 视图显示了 JVM 中垃圾收集器的详细信息
    - Threads Telemetry 视图一般用于显示当前运行线程的个数、守护进程的个数等信息
    - Classes Telemetry 视图一般用于显示已经载入和还没有载入的类的数量
- **快照**（snapshot）
    - 应用程序启动后，profiler 工具开始收集各种执行数据，其中一些数据直接显示在遥测视图中，而另外**大部分**数据被保存在内部，直到用户要求获取快照，基于这些保存的数据的统计信息才被 显示出来。
    - 快照包含了应用程序在一段时间内的执行信息
        - CPU 快照
            - 主要包含了应用程序中函数的**调用关系**及**运行时间**。
        - 内存快照
            - 主要包含了**内存**的分配和使用情况、载入的所有**类**、存在的**对象**信息及对象间的**引用关系**。
- CPU Profiling
    - CPU Profiling 的主要目的是统计函数的调用情况及执行时间，或者更简单的情况就是统计应用程序的 CPU 使用情况。
    - 方式
        - CPU 遥测
        - CPU 快照
- 内存 Profiling
    - 主要通过统计内存使用情况检测可能存在的**内存泄露**问题及确定优化内存使用的方向。
    - 方式
        - 内存遥测
        - 内存快照
- 线程 Profiling
    - 线程 Profiling 主要用于在**多线程**应用程序中确定**内存**的问题所在
        - 某个线程的**状态变化**情况
        - **死锁**情况
        - 某个线程在线程**生命期**内状态的**分布**情况


# 工具
## Java.*
### JPS
- 作用
    - 显示当前用户本地 JAVA 进程及进程号
- 机制
    - java 程序启动后，会在 `java.io.tmpdir` 指定临时目录下，生成名称类似于 `hsperfdata_User` 的文件夹，其中个别文件的名字就是 java 进程的 `pid`
    - 示例
        - `appuser` 用户
        - `ll /tmp/hsperfdata_appuser/`
            ```
            total 928
            -rw------- 1 appuser appuser 32768 May  2 22:17 11337
            -rw------- 1 appuser appuser 32768 May  2 22:17 11489
            ```
- 调用
    - `jps [-q] [-mlvV] [<hostid>]`
    - 参数
        - `-q`
            - 仅显示 `pid` 值
        - `-m`
            - 显示调用时 **main** 函数收到的启动**参数**
        - `-l`
            - 显示启动类的进程 ID 和完整路径名
        - `-v`
            - 显示调用 **JVM** 时的相关**参数**

### JINFO
- 作用
    - 输出进程、Core 文件或远程 Debug 服务器配置信息
        - Java 系统参数
        - 命令行参数
    - 运行于64位虚拟机上时，需指定 `-J-d64`
    - Java **1.8** 版本中**不支持**
- 机制
- 调用
    - `jinfo [option] <pid>`
    - 参数
        - `-flag <name>`         
            - to print the value of the named VM flag
        - `-flag [+|-]<name>`    
            - to enable or disable the named VM flag
        - `-flag <name>=<value>` 
            - to set the named VM flag to the given value
        - `-flags`               
            - to print VM flags
    - 示例
        - `jinfo pid`
            ```
            Caused by: sun.jvm.hotspot.runtime.VMVersionMismatchException: Supported versions are 24.60-b09. Target VM is 25.25-b02
            ```

### JSTAT
- 作用
    - 监视vm内存内的各种**堆**和非堆的**大小**及其**内存使用**量，并可观察classloader，compiler，**gc**相关信息
- 机制
    - 
- 调用
    - `jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]`
    - 参数
        - `option`
            - 选项
                - `–class` 
                    - 监视**类装载**、**卸载数量**、**总空间**及类装载所**耗费时间** 
                - `–gc` 
                    - 监视Java**堆状况**，包括Eden区、2个Survivor区、老年代、永久代等的容量              
                - `–gccapacity` 
                    - 监视内容与`-gc`基本相同，但输出主要关注Java堆各个区域使用到的**最大和最小空间**
                - `–gcutil`
                    - 监视内容与`-gc`基本相同，但输出主要关注已使用空间**占总空间**的百分**比**                 
                - `–gccause` 
                    - 与`-gcutil`功能一样，但是会额外输出导致**上一次GC产生的原因**
                - `–gcnew` 
                    - 监视**新生代GC**的状况                  
                - `–gcnewcapacity` 
                    - 监视内容与`-gcnew`基本相同，输出主要关注使用到的**最大和最小空间**                   
                - `–gcold` 
                    - 监视**老年代GC**的状况                   
                - `–gcoldcapacity` 
                    - 监视内容与`—gcold`基本相同，输出主要关注使用到的**最大和最小空间**                  
                - `–gcpermcapacity` 
                    - 输出**永久代**使用到的**最大和最小空间**                  
                - `–compiler` 
                    - 输出**JIT编译**器编译过的**方法、耗时**等信息
                - `–printcompilation` 
                    - 输出已经**被JIT编译的方法**             
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


### JSTACK
- 作用
    - 观察当前java虚拟机内每一条线程正在执行的**方法堆栈**的集合，以定位线程出现长时间停顿的原因，如线程间死锁、死循环、请求外部资源导致的长时间等待等
    - `java` 程序崩溃生成 `core` 文件，`jstack`工具可以用来获得 `core` 文件的`java stack`和n`ative stack`的信息，从而可以轻松地知道 `java` 程序是如何崩溃和在程序何处发生问题
    - Java **1.8** 版本中**不支持**
- 机制
    - 生成java虚拟机当前时刻的线程快照
    - 观察 Object.Monitor 于线程拥有和区域的情况，获得各进程的相关情况

        |    区域   |                     状态                     |                                            描述                                            |
        |-----------|----------------------------------------------|--------------------------------------------------------------------------------------------|
        | Entry Set | Waiting Thread<br/>Waiting for monitor entry | BLOCKED<br/>waiting for monitor entry<br/>线程进入临界区（ synchronized 保护起来的代码区） |
        | The Owner | Active Thread                                | RUNNABLE                                                                                   |
        | Wait Set  | Waiting Thread<br/>in Object.wait()          | WAITING / TIMED_WAITING <br/> in Object.wait()/ waiting on condition                       |

    - ![thread.bmp](http://otzm88f21.bkt.clouddn.com/e320bd53-2e3e-4387-ba72-18711061a224.bmp)

- 调用
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


### JMAP
- 作用
    - 为进程、核心文件或远程调试服务器打印**共享对象内存**映射或**堆内存**细节
    - Java **1.8** 版本中**不支持**
- 调用
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

### JCONSOLE
- java GUI监视工具，可以以图表化的形式显示各种数据,并可通过远程连接监视远程的服务器VM
- 实时内存、线程、类和系统信息监控，并提供树型 MBean 结构进行属性、方法查询


## Business.*
### TPTP
- 开发商
    - eclipse **官方**的 Profiling 工具插件和可扩展的开发平台框架
- 遥测种类
    - 只提供了**线程 Telemetry**
- 集成性
    - **仅支持 Eclipse**
    - 通过 Eclipse update Manager 或者是安装包进行安装，安装成功后会在 eclipse 中增加相关操作按钮，并支持于 eclipse 中查询 profiling 结果
- CPU快照
    - 包的组成关系，细化到包含的类及类中的方法
    - 方法的调用关系
        - 以每个线程为根节点的方法调用信息，对于树中出现的代表方法的每个节点，列出了该方法的运行时间或运行时间百分比，以及该方法被调用的次数
    - 方法被调用情况
        - 直接调用某方法的其他方法
        - 方法调用该方法的次数及相关运行时间
    - 热点列表
        - CPU 占用时间排列前十的方法、类或包
- 内存快照
    - 包含了类实例的内存分配情况，包括实例化的对象个数，以及这些对象的本身占用内存的大小。
- 源代码定位
    - 只能**定位到某个类**
        - 无法定位到方法或其中的成员变量。
- 快照操作
    - 这些快照不会自动保存，因此当 eclipse 关闭后，这些快照数据将会消失
        - 通过 export 的方式将需要的快照保存下来
- 性能健壮性
    - SAMPLING 模式相关不大
    - **BCI 当前 .6.2无提供**
    - **健壮**

### CodePro Profiler
- 开发商
    - instantiations 公司推出的一款商用 **eclipse 插件**
- 遥测种类：
    - CPU, 内存, 线程, 载入的类以及垃圾收集
- 集成性：
    - **仅支持 Eclipse**
    - Eclipse update Manager 进行安装或者是将安装包直接解压缩后保存在 eclipse 的指定目录下
- CPU快照
    - 包的组成关系，细化到包含的类及类中的方法
    - 方法的调用关系。以树结构表示
        - 类型
            - 以每个线程为根节点的方法调用关系
            - 以整个线程为根节点的方法调用关系
            - 以每个方法为根节点的方法调用关系
        - 对于树中出现的代表方法的每个节点，列出了该方法的运行时间或运行时间百分比，以及由该方法生成的对象个数和为这些对象分配的内存大小
    - 方法的被调用关系。该关系以树结构表示，其中根节点为某个指定的方法
        - 每个节点的子节点为父节点的调用者
    - 热点列表
        - 包含了 CPU 占用时间排前的一些方法
- 内存快照
    - 类实例的内存分配情况，包括实例化的对象个数，以及这些对象的 shallow 和 retained 大小
    - 最大对象列表
        - 包含了 retained 大小排前的一些对象
    - 有可能存在内存泄漏的对象列表：包含了有可能存在内存泄漏的对象以及可能性大小
- 源代码定位
    - 拥有该功能，但是只能定位到类及成员变量，无法定位到方法
- 快照操作
    - 快照会被自动保存在 Eclipse Workspace 之外的一个**临时空间**，但会**随 eclipse 关闭页消失**
    - 提供同类型快照（同 BCI 模式等）比较
- 性能健壮性
    - SAMPLING 模式相关不大
    - BCI 进行 Profilling 时较**慢**
        - 5万行代码需要**5分钟**
    - PROFILLING 较大型程序时，容易出现**栈溢出**情况
            
### YourKit Java Profiler
- 开发商
    - **商用**软件
- 遥测种类
    - CPU, 内存, 线程以及垃圾收集
- 集成性：
    - 支持的操作系统包括
        - Windows
        - Linux
        - FreeBSD
        - Mac OS X
        - Solaris
        - HP-UX
    - 支持的 IDE 
        - Eclipse
        - JBuilder
        - JDeveloper
        - NetBeans
        - Intellij IDEA
    - 用户就可以从 Eclipse 中启动 Profiling，但是 profiling 的结果需要在 YourKit Java Profiler 中进行查询
- CPU快照：
    - 包的组成关系
        - 细化到包含的类及类中的方法
    - 方法的调用关系
        - 以树结构表示，根据根节点表示的对象的不同，分为三种类型：以每个线程为根节点的方法调用关系，以整个线程为根 节点的方法调用关系，以及以每个方法为根节点的方法调用关系。对于树中出现的代表方法的每个节点，列出了该方法的运行时间或运行时间百分比，以及由该方法 生成的对象个数和为这些对象分配的内存大小。
    - 方法的被调用关系
        - 该关系以树结构表示，其中根节点为某个指定的方法，每个节点的子节点为父节点的调用者。
    - 热点列表
        - 包含了 CPU 占用时间排前的一些方法。
- 内存快照	
    - 类实例的内存分配情况，包括实例化的对象个数，以及这些对象的 shallow 和 retained 大小
    - 最大对象列表
        - 包含了 retained 大小排前的一些对象
- 源代码定位
        - 可以定位到类、成员变量及方法。
- 快照操作
    - 针对内存快照提供了自动获取快照的功能，被自动保存到一个临时的文件夹
- 性能健壮性
    - SAMPLING 模式相关不大
    - BCI 进行 Profilling 时**一般**
        - 5万行代码需要**1分钟**
    - **健壮**

### JProfiler
- 开发商
    - ej-technologies 推出的一款**商用**软件
- 遥测种类
    - CPU, 内存 , 线程 , 载入的类以及垃圾收集
- 集成性
    - 支持的操作系统有
        - Windows
        - Linux
        - Mac OS X
        - FreeBSD
        - Solaris
        - AIX
        - HP-UX
    - 支持的 IDE 
        - Eclipse
        - NetBeans
        - Intellij IDEA
        - JBuiler
        - JDeveloper
    - 用户就可以从 Eclipse 中启动 Profiling，但 profiling 的结果需要在 JProfiler 中进行查询
- CPU快照
    - 包的组成关系，细化到包含的类及类中的方法
    - 节点的方法调用关系，以及以每个方法为根节点的方法调用关系。对于树中出现的代表方法的每个节点，列出了该方法的运行时间或运行时间百分比，以及由该方法 生成的对象个数和为这些对象分配的内存大小
    - 方法的被调用关系。该关系以关系图展现，存在复杂结构展示不全情况
    - 热点列表：包含了 CPU 占用时间排前的一些方法
- 内存快照
    - 类实例的内存分配情况，包括实例化的对象个数，以及这些对象的 shallow 和 retained 大小
    - 最大对象列表：包含了 retained 大小排前的一些对象
- 源代码定位
    - 可以定位到类、成员变量及方法
- 快照操作
    - 指定一个目录来保存该 snapshot
- 性能健壮性
    - SAMPLING 模式相关不大
    - BCI 进行 Profilling 时**较无影响**
        - 5万行代码需要**半分钟**
    - 健壮



# 参考
- 整理
    - [JPS](http://domain.yqjdcyy.com/post/java.tools.jps/)
    - [JINFO](http://domain.yqjdcyy.com/post/java.tools.jinfo/)
    - [JSTAT](http://domain.yqjdcyy.com/post/java.tools.jstat/)
    - [JSTACK](http://domain.yqjdcyy.com/post/java.tools.jstack/)
    - [JMAP](http://domain.yqjdcyy.com/post/java.tools.jmap/)