---
title: "Redis.内存映射数据结构.INTSET"
date: "2018-11-29"
categories:
 - "整理"
tags:
 - "Redis"
 - "数据结构"
 - "内存映射"
toc: true
---

# 前置
- 内存映射数据结构
    - 经过特殊编码的**字节序列**
    - **内存消耗**较内部数据结构**少得多**
    - 适用于元素本身**体积较小**的情况

# 定义
- 有序、无重复保存的整数集合
- 集合保存时，使用所存数值的类型
    - 当前类型为16位，而新数值为32位时
    - 先把已有数据类型都先`升级`至32位
    - 再插入新数值

# 特性
- 元素**不重复**
- 元素排列**由小到大**

# 适用场景
- 元素均为**整数**
- 元素**数量不多**


# 结构

| 类     | 属性              | 作用                          |
|--------|-------------------|-----------------------------|
| intset | uint32_t encoding | 保存元素的类型长度            |
|        | uint32_t length   | 存放的元素个数                |
|        | int8_t contents[] | 元素保存数组<br>int8_t 仅占位 |


# 插入

## 流程
- ![REDIS-INTSET-INSERT.png](http://doc.yqjdcyy.com/1b8c4432-1191-4d99-9d14-31cbfc100862.png)

## 示例

| 动作              | 属性     | 值                            |
|-------------------|----------|-------------------------------|
| 初始化            |          |                               |
|                   | encoding | INTSET_ENC_INT16              |
|                   | length   | 0                             |
|                   | content  | 未分配                        |
| 添加 `1`          |          |                               |
|                   | encoding | INTSET_ENC_INT16              |
|                   | length   | 1                             |
|                   | content  | [1]                           |
| 添加 `65535`      |          |                               |
|                   | encoding | INTSET_ENC_INT32<br>**升级**  |
|                   | length   | 2                             |
|                   | content  | [1,65535]                     |
| 添加 `4294967295` |          |                               |
|                   | encoding | INTSET_ENC_INT64<br>**升级**  |
|                   | length   | 3                             |
|                   | content  | [1, 65535, 70000, 4294967295] |


# 升级|扩容

## 流程
- 以上式添加 `65535` 为例
    - 检查插入值的类型范围是否超过当前类型值 `inset.encoding` 范围
    - 如果超过，则更新当前的类型值，并以此重新分配内存= `intset.encoding` * (`intset.length`+ 1)
    - 由后往前，将对应数值转换为 `inset.encoding` 类型数据，并列于预留位置
    - 将 `65535` 插入到数组中

## 图示
- ![REDIS-INTSET-INSERT-BITS.png](http://doc.yqjdcyy.com/5f569775-2e6a-4faf-8eaf-ee37398388cd.png)


## 注意
- `inset.encoding` 由元素中最大值决定
- 不支持降级操作
    - 大数值向小数值的转换是有损的
- 个数限制为 `REDIS_SET_MAX_INTSET_ENTRIES`
    - `REDIS_SET_MAX_INTSET_ENTRIES` 默认为 512

# 参考
- [整数集合](https://redisbook.readthedocs.io/en/latest/compress-datastruct/intset.html)
- [redis/src/intset.c](https://github.com/antirez/redis/blob/2f8912c36ccf5226f7777b132d66cb3f1c4f551b/src/intset.c)