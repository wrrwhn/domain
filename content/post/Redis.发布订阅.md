---
title: "Redis.发布订阅"
date: "2018-12-02"
categories:
 - "整理"
tags:
 - "Redis"
toc: true
---


# 描述
- 支持客户端通过 Redis 实现消息的订阅和发布
    - 支持按频道和按模式
- 消息发布时，将发送到指定渠道和匹配得上的模式


# 结构

| 结构          | 字段            | 类型          | 描述                   |
|---------------|-----------------|---------------|------------------------|
| redisServer   |                 |               |                        |
|               | pubsub_channels | dict *        | `频道`订阅记录         |
|               | pubsub_patterns | list *        | `模式`订阅记录         |
| pubsubPattern |                 |               |                        |
|               | client          | redisClient * | 订阅该模式的客户端列表 |
|               | pattern         | robj *        | 订阅的模式             |


# 命令

| 命令       | 描述                 | 事例                            |
|------------|--------------------|---------------------------------|
| subscribe  | 订阅相关频道         | subscribe channel [channel...]  |
| psubscribe | 订阅指定模式的频道   | psubscribe pattern [pattern...] |
| publish    | 将消息发送至指定频道 | publish channel message         |


# 示例

| 操作      | Publisher               | Subscriber-dev.user                                     | Subscriber-dev.*                                          |
|-----------|-------------------------|---------------------------------------------------------|-----------------------------------------------------------|
| subscribe |                         | subscribe dev.user                                      | psubscribe dev.*                                          |
|           |                         | 1)  "subscribe"<br> 2)  "dev.user"<br> 3) (integer) "1" | 1)  "psubscribe"<br> 2)  "dev.*"<br> 3) (integer) "1"     |
| publish   | publish dev.user "user" | `渠道匹配`                                              | `模式匹配`                                                |
|           | (integer) 2             | 1) "message"<br>2) "dev.user"<br>3) "user"              | 1) "pmessage"<br>2) "dev.*"<br>3) "dev.user"<br>4) "user" |
| publish   | publish dev.book "c++"  |                                                         | `模式匹配`                                                |
|           | (integer) 1             |                                                         | 1) "pmessage"<br>2) "dev.*"<br>3) "dev.book"<br>4) "c++"  |


# 参考
- [订阅与发布](https://redisbook.readthedocs.io/en/latest/feature/pubsub.html)
- [发布/订阅](https://redis.readthedocs.io/en/2.4/pub_sub.html)