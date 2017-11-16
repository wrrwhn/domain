+++
date = "2017-11-16T22:07:46+08:00"
title = "Linux 单TCP连接占用最少内存"
draft = false
tags = ["阅读","Linux","TCP"]
share = true
+++


[TOC]

# [Linux 单 TCP 连接占用最少内存](https://zhuanlan.zhihu.com/p/25241630)

## 结构
- 错
    + TCP 连接建立的时候会分配接收缓冲区和发送缓冲区，各 4KB，一共是 8KB。如果加上 TCP 协议控制块（protocol control block）的 2KB，一共是 10KB。
- 测试
    + >= 2944 B
    + 没有数据时，接收、发送缓冲区是没有数据的

## TCP 解析
### TCP
- 三次握手
    + struct socket_alloc
        * 包含 struct socket/ struct inode
        * 作用于连接 VFS 和 tcp_sock
            - VFS | virtual File System
            - 让open()、read()、write()等系统调用不用关心底层的存储介质和文件系统类型就可以工作的粘合层
- socket 文件缓冲
    + struct dentry
        * struct file `*`
            - 理解对应缓冲时的文件
- 进程调用
    + struct socket_wq
        * wait queue，主要用于阻塞 IO 时挂起当前线程

### 实测 - Least
- 基本消耗
| struct           | size | slab cache name    |
| ---------------- | ---- | ------------------ |
| file             |  256 | "filp"             |
| dentry           |  192 | "dentry"           |
| socket_alloc     |  640 | "sock_inode_cache" |
| tcp_sock         | 1792 | "TCP"              |
| socket_wq        |   64 | "kmalloc-64"       |
| inet_bind_bucket |   64 | "tcp_bind_bucket"  |
| epitem           |  128 | "eventpoll_epi"    |
| tcp_request_sock |  256 | "request_sock_TCP" |

- 额外开销
    + SLAB 额外开销
        * sizeof(struct tcp_sock) == 1792 & 4KB page= 2* tcp_sock => sizeof(struct tcp_sock)= 2048
        * sizeof(dentry) = 192 & 1 page = 21 dentry => sizeof(dentry[10000])= (10000/ 21)* 4= 1908 KB
    + 1 TCP= 1 ephemeral port= 1 inet_bind_bucket & 服务端共享 listening socket.inet_bind_bucket
    + 服务端使用 epoll 处理并发，每添加一个 sockfd 则会创建一个 epitem 对象，大小为 128 B
        * epoll 为 Linux 内核的可扩展 I/O 事件通知机制

### 代码示意图
- [![83595aa6-fc0b-11e6-8a49-0071cc916200.png](http://7xsy59.com1.z0.glb.clouddn.com/83595aa6-fc0b-11e6-8a49-0071cc916200.png)](https://pic4.zhimg.com/v2-2651a980c2ff60ea0260260744eeed83_r.png)
- [![3e4e5d06-fc0f-11e6-8d85-0071cc916200.png](http://7xsy59.com1.z0.glb.clouddn.com/3e4e5d06-fc0f-11e6-8d85-0071cc916200.png)](https://pic1.zhimg.com/v2-a13d7d010ea9fce68b41c96e1ce31b14_b.png)
