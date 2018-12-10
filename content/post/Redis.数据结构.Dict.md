---
title: "Redis.数据结构.Dict"
date: "2018-11-28"
categories:
 - "整理"
tags:
 - "Redis"
 - "数据结构"
toc: true
---


# 结构
## 定义

| 结构      | 字段                                          | 作用                                                                        |
|-----------|-----------------------------------------------|-----------------------------------------------------------------------------|
| dict      |                                               |                                                                             |
|           | dictType *type                                | 类型处理函数                                                                |
|           | void *privdata                                | 类型处理函数时的数据                                                        |
|           | dictht ht[2]                                  | 哈希表 `dictht` 数组<br>ht[0]默认存储数据，ht[1]于实现渐近式 `rehash` 时使用 |
|           | int rehashidx                                 | `rehash` ht[0]的渐近处理进度                                                |
|           | int iterators                                 | 正在**安全**运行的**迭代器数量**<br>为**0**时才允许进行 `rehash` 操作       |
| dictht    |                                               |                                                                             |
|           | dictEntry **table                             | 哈希表节点指针数组                                                          |
|           | unsigned long size                            | 指针数组大小                                                                |
|           | unsigned long sizemask                        | 数组大小掩码，= size-1<br>用于计算索引值                                     |
|           | unsigned long used                            | 当前表中包含节点数量                                                        |
| dictEntry |                                               |                                                                             |
|           | void *key                                     | 键；`void *`表示任何类型                                                     |
|           | union {void *val;uint64_t u64;int64_t s64;} v | 值                                                                          |
|           | struct dictEntry *next                        | 下一节点指针                                                                |


## 结构
- ![REDIS-DB-DICT.png](http://doc.yqjdcyy.com/1e4f514e-8611-4879-aede-7ae46a462d49.png)

# 添加

- 判断点
    - 是否存在该键
        - 碰撞算法为 **链接法**
    - Ht[0] 是否已初始化
    - Ht[0] 是否需要 ReHash
    - 是否正在执行 ReHash

- 流程
    - ![REDIS-DB-DICT-ADD.png](http://doc.yqjdcyy.com/e403d127-6a22-4426-96a8-6ba5287a92f6.png)


# 碰撞算法
## 开放寻址法

- 流程
    - 计算键的哈希值，并与数组长度取余得到存放的数组下标值
        - array.idx = hash(key)% array.size
    - 如若数组对应位置处为空，将数值保存到此即可
    - 如若数组对应位置已存放数值，则一直下称光标，直到找到未存放数值的位置

- 图示
    - ![REDIS-HASH-开放寻址.png](http://doc.yqjdcyy.com/9dc63220-1f4d-417e-9921-d9891111a400.png)


## 二次探测法

- 描述
    - 开放寻址法的升级版本
    - 在检测到对应位置上已存放数值的情况，先行向下循环移动当前下标的平方值的格数
        - [hash(key)+ array.idx^ 2]% array.size

- 图示
    - ![REDIS-HASH-二次探测.png](http://doc.yqjdcyy.com/8645a044-530a-4aec-bd94-ae4f15cafda7.png)

## 链接法

- 流程
    - 获取键的计算下标后，直接将值加入当前数组下标所指向的键值对象链表即可
        - 默认添加在链表首部
            - 减少尾部偏移时间
            - **Redis 所选择的算法**

- 图示
    - ![REDIS-HASH-链接法.png](http://doc.yqjdcyy.com/3b74d8d4-3568-48d2-ba5e-972254850aab.png)



# 扩容

## 前置
- 主要判断比率，哈希表大小和节点数量
    - 当数值为 `1: 1`时，说明基本可以通过 `hash(key)` 一次定位到对应值
    - 当数值远小于1时，则相当于在多个链表中查询
    
## 条件
- radio= ht[0].uesd/ ht[0].size

| 场景        | 条件                             | 备注                                         |
|-----------|----------------------------------|--------------------------------------------|
| 自然 ReHash | radio> 1 & `dict_can_resize`     | `dict_can_resize` 主要于**持久化时暂时关闭** |
| 强制 ReHash | radio> `dict_force_resize_ratio` | `dict_force_resize_ratio` 默认为 **5**       |

## 过程

- 初始化 ht[1].table 大小大于 ht[0].size
    - ht[1].size 为大于 `DICT_HT_INITIAL_SIZE`* 2^n，且大于 `2`* `ht[0].used`
- 将 ht[0].table 的键渐近式地迁移至 ht[1].table
    - dict.rehashidx++
    - ht[0].used--
    - ht[1].used++
- ht[0]中所有数据清空后，将 ht[1] 替换为 ht[0]
    - ht[0] clear
    - ht[0]= ht[1]
    - dict.rehashidx= -1


## 流程

- 初始状态
    - ![REDIS-DB-DICT.png](http://doc.yqjdcyy.com/e82f18d1-2907-4e7c-bb2a-631856f4ece1.png)

- rehashing
    - ![REDIS-DB-DICT-REHASHING.png](http://doc.yqjdcyy.com/188369c4-3a21-43a0-b2ae-d9742c9761b2.png)

- 结束状态
    - ![REDIS-DB-DICT-REHASHED.png](http://doc.yqjdcyy.com/9d8ad998-a69a-4e77-868f-b200938cff2f.png)


## 调用函数

- `_dictRehashStep`
    - rehashing 情况下执行，将 ht[0].table **第一个非空索引**上的链表均迁移到 ht[1].table 上

- `dictRehashMilliseconds`
    - 指定毫秒内对字典进行 rehash 操作
    - 由 ServerCron 调用执行

# 收缩

## 时机

- 当 `ht[0].used* 100/ ht[0].size < REDIS_HT_MINFILL` 时触发
    - REDIS_HT_MINFILL 默认为 10
    - 即实际使用率小于 10% 时触发

## 与扩容的差异
- 流程类似扩容，差异于初始化 Ht[1].table 时，大小为 `DICT_HT_INITIAL_SIZE* 2^n`，且大于 `ht[0].used`

    ```c
    // 调用
    return dictExpand(d, d->ht[0].used*2)
    return dictExpand(d, minimal = d->ht[0].used);

    // 实现
    static unsigned long _dictNextPower(unsigned long size)
    {
        unsigned long i = DICT_HT_INITIAL_SIZE;

        if (size >= LONG_MAX) return LONG_MAX + 1LU;
        while(1) {
            if (i >= size)
                return i;
            i *= 2;
        }
    }
    ```

# 参考
- [字典](https://redisbook.readthedocs.io/en/latest/internal-datastruct/dict.html)
- [深入理解哈希表](https://bestswifter.com/hashtable/)
- [HASH表的实现（拉链法）](https://www.cnblogs.com/lizhanwu/p/4303410.html)
- [散列表-链接法和开放寻址法 线性探查](https://blog.csdn.net/lady_lili/article/details/52374248)
- [构造哈希表之二次探测法](https://blog.csdn.net/xyzbaihaiping/article/details/51607770)
- [redis数据结构之dict详解](https://my.oschina.net/zhangxufeng/blog/841086)
- [Redis字典的实现](https://www.jianshu.com/p/1137a2e694c8)