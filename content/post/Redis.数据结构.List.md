---
title: "Redis.数据结构.List"
date: "2018-11-29"
categories:
 - "整理"
tags:
 - "Redis"
 - "数据结构"
toc: true
---


# 结构

| 类       | 属性                               | 作用                                         |
|----------|------------------------------------|----------------------------------------------|
| List     |                                    |                                              |
|          | listNode *head                     | 链头指针                                     |
|          | listNode *tail                     | 表尾指针                                     |
|          | unsigned long len                  | 链表项数量                                   |
|          | void *(*dup)(void *ptr)            | 复制函数<br>`value = copy->dup(node->value)` |
|          | void (*free)(void *ptr)            | 删除函数                                     |
|          | int (*match)(void *ptr, void *key) | 匹配函数                                     |
| ListNode |                                    |                                              |
|          | struct listNode *prev              | 前一节点指针                                 |
|          | struct listNode *next              | 后一节点指针                                 |
|          | void *value                        | 数值                                         |

# 图示
- ![REDIS-LIST.png](http://doc.yqjdcyy.com/dc0319a3-28f8-4ea6-b54b-285eef30f703.png)

# 使用原则
- 优先使用压缩列表，优化内存占用

# 使用场景
- 事务模块中的命令、时间事件保存
- 服务器中客户端的保存
- 订阅/发送模块中存储订阅模式中的客户端列表

# 迭代器
- 结构

    | 类       | 属性           | 作用                                                     |
    |----------|----------------|----------------------------------------------------------|
    | listIter |                |                                                          |
    |          | listNode *next | 下一指针                                                 |
    |          | int direction  | 迭代方向<br>AL_START_HEAD=0，正序<br>AL_START_TAIL=1，倒序 |

# 参考
- [双端链表](https://redisbook.readthedocs.io/en/latest/internal-datastruct/adlist.html)
- [redis/src/adlist.c](https://github.com/antirez/redis/blob/unstable/src/adlist.c)