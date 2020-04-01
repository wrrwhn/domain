---
title: "Java.DirectByteBuffer 10%"
date: "2019-09-23"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---


# 思考 
- 什么是堆外内存
    - 与堆内内存的差别
- 具体的使用场景
- 是否会引起 GC 和 OOM
    - 不进行GC 标记移动时，JNI 进行操作，导致数据错乱
    - 堆内外拷贝时，JVM保证不进行 GC


# 相关知识
## 虚引用
- 无法返回引用对象，仅用于跟踪对象被 GC 回收的状态
    - 查看是否加入了 ReferenceQueue 来触发相应的回收操作
- 更多引用知识，详见 [Java.Reference](http://domain.yqjdcyy.com/post/java.reference/)

## 状态

| 状态     | 权限/操作                    |
|--------|--------------------------|
| 内核态   | 硬件资源操作，如 Socket/ IO等 |
| 系统调用 | 内核封装的请求接口           |
| 用户态   | 应用程序活动的空间           |

## CPU 的模式配置
- intel cpu 提供 `Ring0-3` 四种级别的运行模式，且 0 为最高级别
    - 其中 `Ring0` 为**内核态**，而 `Ring3` 为**用户态**
- Ring3权限低于 Ring0，无法访问 Ring0 的地址空间（代码/数据）
    - 需要将状态切换为内核态，操作完成后再切换回用户太（类似 linux.su ）

## JNI 读写数据
### 堆内-堆外-文件
- FileChannelImpl.read
    - File-> 堆外-> 堆内
    - 过程中，JVM 保证不会进行 GC 操作

### 堆外-文件
- DirectByteBuffer
    - 堆外-> File
    - 程序通过 JNI 直接操作堆外分配的内存地址

# 代码
## FileChannelImpl
- 架构
    - ![FileChannelImpl.png](http://doc.yqjdcyy.com/a9e15217-128b-4c6c-bded-d94b3e5ae5ff.png)


## DirectByteBuffer
- 架构
    - ![DirectByteBuffer.png](http://doc.yqjdcyy.com/b3a60fcb-3f19-46c6-b0d8-7e81d4a30490.png)


# 参考
- []()
- []()
- []()
- []()
- []()
- []()