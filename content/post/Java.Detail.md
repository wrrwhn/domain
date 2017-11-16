+++
date = "2017-08-09T22:07:46+08:00"
title = "Java.Detail"
draft = false
tags = ["整理","Java"]
share = true
+++


[TOC]

## finally
- 用于及时释放资源以不影响系统性能
- 遵循最晚申请，最早释放原则，就近释放
    - 若在A类一方法中申请到某个资源，应该就在这个方法的末尾将资源释放掉

## preparedStatement
- 预编译语句，继承自Statement，用于处理大业务量的数据库操作
- 效率高，但代价是建立在创建对象时付出较大的成本，但是创建后可以通过传入不同的参数`重复高效`的使用
- 示例
    ```
    PreparedStatement ps = con.prepareStatement("select * from student where stuNo=?");                //!!
    for(int i=0; i< 1000; i++){
        ps.setString(1, "1000");
        ResultSet rs = ps.executeQuery();
        rs.close();
    }
    ps.close();                    //!!
    ```
- 注
    - 在高频率反复执行同一类业务量的数据处理任务时使用


## 连接池
- 为服务器配置连接池，而在配置文件中指定所要连接的数据库和连接个数
- 便于服务器启动时，首先连接目标数据库创建指定个数的活动连接，并将其放到连接池中
- 优点
    - 不同的访问者共享有限的连接，免去了数据库连结的漫长创建过程
- 类似使用
    - 在线程管理中，为避免线程资源的浪费和提高效率，也可以采用线程池来进行线程的管理


## 内存管理
### 引用类型
#### 弱引用
- 当被弱引用指向的对象除该弱引用之外不再有其他的引用指向这个对象，这个对象即为“弱引用”
- 回收标准
    - 检查到该弱引用指向的对象，就会将其回收
- 支持对象
    - WeakRefrence：弱引用对象
    - WeakHashMap：专用于管理弱引用集合，消除由于`HashMap不释放`导致对象数据长期残留于内存的情况   
- 事例
    ```
    Sample sam = new Sample();
    WeakReference wr = new WeakReference(sam);  // 定义弱引用并指向创建的对象
    sam = null;                                 // 使对象变成了垃圾
    ((sam)wr.get()).shout();                    // 验证对象是否还存在
        System.gc();                            // 执行垃圾收集
    try{
        Thread.sleep(80);                       // 主线程休眠，垃圾回收器运行
    }catch(Exception e){
        e.printStackTrace();
    }
    ```
- 用途
    - 用于防止内存泄露

#### 软引用
- 当被软引用指向的对象除软引用外部存在其他的引用指向这个对象，这个对象即为“软引用”
- 回收标准
    - 在内存即将耗尽的情况下，垃圾收集器在抛出异常之前尝试回收软引用指向对象的内存
- 支持对象
    - SoftRefrence
- 事例
    ```
    Sample sam = new Sample();
    SoftRefrence sr = new SoftRefrence(sam);    // 定义弱引用并指向创建的对象
    sam = null;                                 // 使对象变成了垃圾
    ((sam)sr.get()).shout();                    // 验证对象是否还存在
        System.gc();                            // 执行垃圾收集
    try{
        Thread.sleep(80);                       // 主线程休眠，垃圾回收器运行
    }catch(Exception e){
        e.printStackTrace();
    }
    ```
- 用途
    - 作为缓冲区           

#### 幻影引用
- 所指向的对象和一般的垃圾对象一样会被回收，并不能延长存活时间
- 特点
    - 必须和一个ReferenceQueue类一起使用。
    - 当一个被幻影引用指向的对象被垃圾收集器检测到需要回收时，垃圾收集器会将其挂到ReferenceQueue队列中，以起到消息传递的作用，并使对象内存在被回收前执行指定的pre-mortem操作                                   

### 小肥猪问题
- 一般出现在企业级服务器程序中，这些服务程序在起到的时候会创建一些对象，而这些对象在服务器运行中会产生新的对象。
- 这些新产生的子对象如果没有及时在用完之后释放掉，就会衣服在创建它们的对象上，而这些对象便是“小肥猪”


## String
- 对String对象使用+，实质上创建很多无用的中间对象，增加了系统的开销。
- 推荐方法
    - StringBuffer.append("..");
    - StringBuilder
