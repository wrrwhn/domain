---
title: "Redis.数据结构.SkipList"
date: "2018-11-29"
categories:
 - "整理"
tags:
 - "Redis"
 - "数据结构"
toc: true
---


# 结构

| 类             | 属性                    | 作用                                          |
|----------------|-------------------------|-----------------------------------------------|
| zskiplist      |                         |                                               |
|                | zskiplistNode *header   | 头结点                                        |
|                | zskiplistNode *tail     | 尾结点                                        |
|                | long length             | 链表结点数量                                  |
|                | int level               | 链表内节点的最大层数                          |
| zskiplistNode  |                         |                                               |
|                | robj *obj               | 成员对象<br>RedisObject 类型，Redis 的通用类型 |
|                | double score            | 分值                                          |
|                | zskiplistNode *backward | 后退节点指针                                  |
|                | *zskiplistLevel level   | 关联层列表                                    |
| zskiplistLevel |                         |                                               |
|                | zskiplistNode *forward  | 前进节点指针                                  |
|                | unsigned int span       | 当前层的节点总数                              |


# 囷示
- ![REDIS-SKIPLIST.png](http://doc.yqjdcyy.com/9d25eb5f-5856-48ec-b127-cc016ec23e72.png)


# Redis 实现

## 场景
- 用于实现**有序集**数据结构

## 自定义
- 允许**重复**的 Score 值
- 比较时，需**同时比对 Member 值和 Score 值**
- 定时时，创建有单层级的**后退指针**


## 层级限制

- 随机生成当前层级，由下往上，每次通过的机会为 50%

    ```c
    int zslRandomLevel(void) {
        int level = 1;
        while ((random()&0xFFFF) < (ZSKIPLIST_P * 0xFFFF))
            level += 1;
        return (level<ZSKIPLIST_MAXLEVEL) ? level : ZSKIPLIST_MAXLEVEL;
    }
    ```

- 默认值为 `ZSKIPLIST_MAXLEVEL`= **64**


## 查找
- ![REDIS-SKIPLIST-FIND.png](http://doc.yqjdcyy.com/a25b50c9-7a70-45f1-ad03-4b28e2eab083.png)


## 新增
- ![REDIS-SKIPLIST-ADD.png](http://doc.yqjdcyy.com/6c465035-004b-40b6-adab-1a7dee1eff82.png)



# 参考
- [跳跃表](https://redisbook.readthedocs.io/en/latest/internal-datastruct/skiplist.html)
- [什么是跳跃表](http://blog.jobbole.com/111731/)
    - 建立跳跃表概念
- [redis/src/t_zset.c](https://github.com/antirez/redis/blob/6a6471aad5e4f8d6cbab677b918b14cdee416296/src/t_zset.c)
    - 表操作
- [Redis内部数据结构详解(3)——robj](http://zhangtielei.com/posts/blog-redis-robj.html)
- [浅析SkipList跳跃表原理及代码实现](https://blog.csdn.net/ict2014/article/details/17394259)