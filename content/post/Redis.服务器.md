---
title: "Redis.服务器"
date: "2018-11-26"
categories:
 - "整理"
tags:
 - "Redis"
toc: true
---


# 初始化
## 初始化全局状态
- `数据库列表`
- `命令表`
    - 执行命令时，映射命令字符对应的实现函数
- `网络连接`
    - 套接字地址
    - 端口
    - 套接字描述符
- 连接的`客户端列表`
- `Lua` 脚本运行`环境`
- 订阅与发布
- `日志`与慢日志
- 数据`持久化`
- 服务器`配置`
    - 数据库数量
    - 是否以守护进程运行
    - 最大连接客户端数量
- `统计`信息
    - 运行时间
    - 内存占用
    - 键值命中情况

## 载入配置文件
- 配置内容
    - 套接字`端口`
    - 数据库`数量`
    - `内存限制`
    - 内存`回收`策略
    - 持久化策略

- 调整方式
    - 配置文件
    - 显式选项
        - 覆盖配置文件的值

## 创建守护进程

## 初始化功能模块
- 流程
    - 为服务变量的数据结构分配内存
    - 初始化数据结构
        - 完成**数据结构的初始化**，相关于完成相应**功能的初始化**
        - 如完成订阅与发布所需的链表的内存分配后，该功能即准备就绪，可供服务使用
    - 步骤一相关于`定义`变量名，而此处为`实例化`变量

- 功能
    - `信号`
    - 日志
    - 客户端
    - 共享
    - `事件`
    - 数据库
    - 网络连接
    - 订阅与发布
    - 统计变量
    - `定时任务`
    - AOF 读取
    - 内存限制
    - Lua 环境
    - 慢查询
    - 后台

- 完成标志
    - 界面输出 Redis 的码 Logo、版本信息

## 载入数据
- 操作
    - 读取 `RDB|AOF` 文件
    - 加载至内存
- 完成标志
    - `[6717] 22 Feb 11:59:14.830 * DB loaded from disk: 0.068 seconds`

## 事件循环
- 操作
    - 开启事件循环
- 完成标志
    - `[6717] 22 Feb 11:59:14.830 * The server is now ready to accept connections on port 6379`


# 组成
- Share Objects
- Command Table
- Lua Environment
- Databases
    - DB1...DBn
- Clients
    - C1...Cn
- AOF|RDB
- Event Handler
    - File
    - Time

# 客户端请求

## 连接
### 流程
- 端通过`文件事件`，`无阻塞`地响应客户端的连接，并返回`套接字描述符` fd
- 为 fd 创建 redisClient 结构实例，并添加至`客户端链表`
- 在事件处理中，为该 fd 关联文件事件

### RedisClient 结构
- 数据
    - 套接字描述符
    - 当前连接的数据库号码和指针
    - 指向命令函数的指针
    - 当前状态
- 缓存
    - 查询
    - 回复
- 数据结构
    - 事务，如 `MULTI` 和 `WATCH`
    - 阻塞，如 `BLPOP` 和 `BPROPLPUSH`
    - 订阅与发布，如 `PUBLISH` 和 `SUBSCRIBE`
- 统计
    - 创建时间
    - 最后交互时间
    - 缓存大小


## 调用
- ![REDIS-INVOKE.png](http://doc.yqjdcyy.com/88134ab1-9bbd-45fa-b257-6546b61404cb.png)


# 参考
- [服务器与客户端](https://redisbook.readthedocs.io/en/latest/internal/redis.html)