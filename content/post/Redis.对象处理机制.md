---
title: "Redis.对象处理机制"
date: "2018-12-02"
categories:
 - "整理"
tags:
 - "Redis"
toc: true
---

# 机制
- 使用 `RedisObject` 类型对象支持
- 支持进行类型检测
- 执行相同命令时，支持针对键所对应值类型进行多态处理
    - 集合存在 `LinkedList` 和 `ZipList` 两种形式实现，但执行 `ZADD` 命令时，不需要关心具体使用的编码
- 通过 `refcount` 变量以支持共享和销毁


# 结构

| 对象        | 字段              | 描述                                                             |
|-------------|-------------------|------------------------------------------------------------------|
| redisObject |                   |                                                                  |
|             | unsigned type     | 存储的数据类型                                                   |
|             | unsigned notused  | 对齐位                                                           |
|             | unsigned encoding | 当前使用的编码值                                                 |
|             | unsigned lru      | Least Recentyly Used<br>最近最少使用<br>用于内存不足时的回收算法 |
|             | int refcount      | 引用计数<br>负责维持和销毁对象                                   |
|             | void *ptr         | 关联对象指针<br>根据 `type` 和 `encoding` 指向实际存储的数据结构 |


# 枚举
## 类型

|     类型     | 值 |  描述  |
|--------------|----|--------|
| REDIS_STRING |  0 | 字符串 |
| REDIS_LIST   |  1 | 列表   |
| REDIS_SET    |  2 | 集合   |
| REDIS_ZSET   |  3 | 有序集 |
| REDIS_HASH   |  4 | 哈希表 |

## 编码

| 编码                      | 值 | 描述     |
|---------------------------|----|----------|
| REDIS_ENCODING_RAW        | 0  | 字符串   |
| REDIS_ENCODING_INT        | 1  | 整数     |
| REDIS_ENCODING_HT         | 2  | 哈希表   |
| REDIS_ENCODING_ZIPMAP     | 3  | zipmap   |
| REDIS_ENCODING_LINKEDLIST | 4  | 双端链表 |
| REDIS_ENCODING_ZIPLIST    | 5  | 压缩列表 |
| REDIS_ENCODING_INTSET     | 6  | 整数集合 |
| REDIS_ENCODING_SKIPLIST   | 7  | 跳跃表   |

# 实现

| 数据类型     | 编码类型                  | 补充                                                             |
|--------------|---------------------------|------------------------------------------------------------------|
| REDIS_STRING | REDIS_ENCODING_INT        | Long 型整数                                                      |
|              | REDIS_ENCODING_EMBSTR     | 使用 `embstr` 编码<br>                                           |
|              | REDIS_ENCODING_RAW        | SDS-简单动态字符串                                               |
| REDIS_LIST   | REDIS_ENCODING_LINKEDLIST |                                                                  |
|              | REDIS_ENCODING_ZIPLIST    | 元素内容长度小于 64B<br>无数数量少于 512                         |
| REDIS_SET    | REDIS_ENCODING_HT         |                                                                  |
|              | REDIS_ENCODING_INTSET     | 所有元素无语为整数<br>元素数量不超过512<br>512为 CPU/内存 权衡值 |
| REDIS_ZSET   | REDIS_ENCODING_SKIPLIST   | 由 Dict + ZSkipList 实现<br>前者保证查询快速定位，后者保证排序    |
|              | REDIS_ENCODING_ZIPLIST    | 元素数量少于 128 个<br>元素内存长度均小于 64 B                   |
| REDIS_HASH   | REDIS_ENCODING_ZIPLIST    | 键与值的长度小于4B<br>键值对数量少于512                          |
|              | REDIS_ENCODING_HT         |                                                                  |

# 多态调用
## 注意点
- 类型判断
- 多态调用

## 流程
- ![REDIS-OBJECT-INVOKE.png](http://doc.yqjdcyy.com/d736d3bc-c781-4cb9-a7a4-b896743b4dce.png)

# 共享对象

## 原因
- 预先为常见值对象创建、分配，减少频繁重复分配

## 实现
- 使用享元模式实现

## 适用对象
- 命令返回值
    - 成功时的 `OK`
    - 错误时的 `ERROR`
    - 类型错误时的 `WRONGTYPE`
    - 事务返回时的 `QUEUED`
    - ...

- 小于 `REDIS_SHARED_INTEGERS` 的所有整数
    - `REDIS_SHARED_INTEGERS` 默认为 `10000`

# 补充
## EMBSTR 为什么限制 39 字节
- embstr 类型由 redisObject 和 sdshdr 组合而成
- 各元素占位情况
    = 25B+ ？

    | 结构        | 属性     | 类型         | 大小   | 描述                 |
    |-------------|----------|--------------|--------|----------------------|
    | redisObject |          |              |        |                      |
    |             | type     | unsigned     | 4b     | 定义时指定           |
    |             | notused  | unsigned     | 2b     | 定义时指定           |
    |             | encoding | unsigned     | 4b     | 定义时指定           |
    |             | lru      | unsigned     | 22b    | 定义时指定           |
    |             | refcount | int          | 4B     |                      |
    |             | *ptr     | void         | 8B     | 此外为 64 为操作系统 |
    |             |          |              | =16B   |                      |
    | sdshdr      |          |              |        |                      |
    |             | len      | unsigned int | 4      |                      |
    |             | free     |              | 4      |                      |
    |             | buf      | char[]       | ?      |                      |
    |             | `\0`     | char         | 1      |                      |
    |             |          |              | =9B+ ? |                      |

- v2.4+，使用 jemalloc 分配内存，大小为 8B，16B，32B和 64B
    - 分配最大额度内存 64B 情况下，剩余可存储空间大小为 `39B`
    - 当超过时，将使用不同内存页存储，读取效率将下降
        - 个人理解，无实际文档支持


## EMBSTR 相较 RAW 的优势

- 两种编码的区别，在于其 `redisObject` 结构与 `sdshdr` 结构是否**内存连续**

|  比较项  |          EMBSTR          |                    RAW                     |
|----------|--------------------------|--------------------------------------------|
| 内存分配 | `1 次`                   | 2 次<br>分别用于申请 redisObject 和 sdshdr |
| 内存释放 | `1 次`                   | 2 次<br>分别释放 redisObject 和 sdshdr     |
| 缓存     | `连续内存`，便于利用缓存 |                                            |


# 参考
- [对象处理机制](https://redisbook.readthedocs.io/en/latest/datatype/object.html)
- [对象的类型与编码](http://redisbook.com/preview/object/object.html)
- [哈希对象](http://redisbook.com/preview/object/hash.html)
- [字符串对象](http://redisbook.com/preview/object/string.html)
- [有序集合对象](http://redisbook.com/preview/object/sorted_set.html)
- [LRU原理和Redis实现](https://zhuanlan.zhihu.com/p/34133067)
- [Redis中数据类型和内部编码的关系](https://nullcc.github.io/2018/01/23/Redis%E4%B8%AD%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E5%92%8C%E5%86%85%E9%83%A8%E7%BC%96%E7%A0%81%E7%9A%84%E5%85%B3%E7%B3%BB/)
- [关于 Redis 字符串小于 39 字节的疑惑](https://segmentfault.com/q/1010000002388947/a-1020000003509960)