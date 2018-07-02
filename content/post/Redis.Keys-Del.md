---
title: "Redis.Keys-Del"
date: "2017-10-16"
categories:
 - "整理"
tags:
 - "Redis"
toc: true
---


# 参考
- [Linux 系統 xargs 指令範例與教學](https://blog.gtwang.org/linux/xargs-command-examples-in-linux-unix/)
- [获取Redis所有Key / 删除指定前缀的Key](http://www.redisfans.com/?p=71)
- [批量删除Redis数据库中的Key](http://blog.csdn.net/spring21st/article/details/15771861)

# 明细
- Login
    - `/opt/redis/redis-cli -p <port> -a <password>`
- Search-Delete
    - `/opt/redis/redis-cli keys "*" | xargs /opt/redis/redis-cli del`

# 示例
## 删除指定练习相关缓存
- 导出以指定键开头的缓存
    - `/usr/local/bin/redis-cli -p 37652 -a 5c\!1@a7z#5623qb2eee98e12b16d301466yunkai1024 keys "exercises:10203*" > /data/op-script/redis/exercises:10203`
- 列举所有以指定键开头的缓存
    - `/usr/local/bin/redis-cli -p 37652 -a 5c\!1@a7z#5623qb2eee98e12b16d301466yunkai1024 keys "exercises:10203*"`
- 删除所有以指定键开头的缓存
    - `/usr/local/bin/redis-cli -p 37652 -a 5c\!1@a7z#5623qb2eee98e12b16d301466yunkai1024 keys "exercises:10203*" | xargs /usr/local/bin/redis-cli -p 37652 -a 5c\!1@a7z#5623qb2eee98e12b16d301466yunkai1024 del`

