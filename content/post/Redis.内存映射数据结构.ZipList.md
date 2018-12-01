---
title: "Redis.内存映射数据结构.ZipList"
date: "2018-11-30"
categories:
 - "整理"
tags:
 - "Redis"
 - "数据结构"
 - "内存映射"
toc: true
---

# 描述
- 由一系列特殊编码的内存块构成的列表


# 适用
- Hash.Node
- List.Node
- SortSet.key


# 结构

| 定义域  | 属性             | 占用大小 | 描述                                                                                                                      |
|---------|------------------|----------|---------------------------------------------------------------------------------------------------------------------------|
| Header  |                  |          |                                                                                                                           |
|         | zlbytes          | 4B       | ziplist 整体所占用的内存字节数<br>供内存分配、快速定位使用                                                                 |
|         | zltail           | 4B       | 相对到表尾节点的偏移字节数                                                                                                |
|         | zllen            | 2B       | 内部存储的节点数量<br> 当元素数量超过 2^16 时，需遍历获得                                                                  |
| Entry[] |                  |          |                                                                                                                           |
|         | pre_entry_length | 1/5B     | 计算前一节点的长度（encoding+ length+ content)<br>1B：节点长度小于 254 的情况<br>5B：首节点值为 254，剩余四个字节保存实际长度 |
|         | encoding         | 2b       | 00/01/10 代表不同 length 的字符数组<br>11 代表保存的为整数                                                                |
|         | length           | 6/14/40b | 详见[数据类型](?#数据类型)                                                                                                |
|         | content          |          |                                                                                                                           |
| End     |                  |          |                                                                                                                           |
|         | zlend            | 1B       | 固定标识<br>值为 `255` 的二进制值 `1111 1111`                                                                             |

# 数据类型

- 类型遍历

| Encoding | Length                                     | 占用内存 | 存储内存值                  |
|----------|--------------------------------------------|----------|--------------------------|
| 00       | bbbbbb                                     | 1B       | char[]<br>长度小于 `2^6`    |
| 01       | bbbbbb bbbbbbbb                            | 2B       | char[]<br>长度小于 `2^14`   |
| 10       | ______ bbbbbbbb bbbbbbbb bbbbbbbb bbbbbbbb | 5B       | char[]<br>长度小于 `2^40`   |
| 11       | 00 0000                                    | 1B       | int16_t<br>int 16bit 有符号 |
|          | 01 0000                                    | 1B       | int32_t<br>int 32bit 有符号 |
|          | 10 0000                                    | 1B       | int64_t<br>int 64bit 有符号 |
|          | 11 0000                                    | 1B       | int 24bit 有符号            |
|          | 11 1110                                    | 1B       | int 8bit 有符号             |
|          | 11 bbbb                                    | 1B       | int 4bit **无**符号         |

- 符号含义

| 符号 |      含义      |
|------|----------------|
| `0`  | 二进制 `0`     |
| `1`  | 二进制 `1`     |
| `_`  | 空位           |
| `b`  | 实际二进制数值 |


# 操作
## 追加
- 获取 `zlbytes` 值
- 根据追加值，计算所需空间大小，并为 ziplist 重新分配内存
- 设置新 `entry` 的各属性值
- 更新 `zlbytes`、`zltail` 和 `zllen` 值

## 插入
- 与「追加」有异的情况
    - 图示位置
        - |prev|new|next|...|
    - 当 `prev` 长度与 `new` 长度不一致时，需要级联更正 `next` 及后续节点的 `pre_entry_length` 信息
        - 操作复杂度为 `O(N^2)`
        - 所有节点的长度均接近 254 的极小概率情况

## 删除
- 与插入一样，有可能导致 `next` 及后面的节点连琐更新


# 示例

| 操作             | 字段                                     | 内存大小 | 内容值                             |
|------------------|------------------------------------------|----------|------------------------------------|
| 初始化           |                                          |          |                                    |
|                  | zlbytes                                  | 4B       | 00010000                           |
|                  | zltail                                   | 4B       | 00001010                           |
|                  | zllen                                    | 2B       | 0                                  |
|                  | zlend                                    | 1B       | 11111111                           |
| 添加 "Hello"     |                                          |          |                                    |
|                  | zlbytes                                  | 4B       | 00010010<br>更新整体长度           |
|                  | zltail                                   | 4B       | 00001010<br>仅一元素，不需要更新    |
|                  | zllen                                    | 2B       | 1<br>更新元素个数                  |
|                  | entry                                    |          |                                    |
|                  | &nbsp;&nbsp;&nbsp;&nbsp;pre_entry_length | 1B       | 00000000                           |
|                  | &nbsp;&nbsp;&nbsp;&nbsp;encoding         | 2b       | 00                                 |
|                  | &nbsp;&nbsp;&nbsp;&nbsp;length           | 6b       | 000101                             |
|                  | &nbsp;&nbsp;&nbsp;&nbsp;content          | 5B       | Hello                              |
|                  | zlend                                    | 1B       | 11111111                           |
| 表头插入 “world” |                                          |          |                                    |
|                  | zlbytes                                  | 4B       | 00011001<br>更新整体长度           |
|                  | zltail                                   | 4B       | 00010001<br>更新至末尾元素的偏移量 |
|                  | zllen                                    | 2B       | 2<br>更新元素个数                  |
|                  | entry                                    |          |                                    |
|                  | &nbsp;&nbsp;&nbsp;&nbsp;pre_entry_length | 1B       | 00000000                           |
|                  | &nbsp;&nbsp;&nbsp;&nbsp;encoding         | 2b       | 00                                 |
|                  | &nbsp;&nbsp;&nbsp;&nbsp;length           | 6b       | 000101                             |
|                  | &nbsp;&nbsp;&nbsp;&nbsp;content          | 5B       | world                              |
|                  | entry                                    |          |                                    |
|                  | &nbsp;&nbsp;&nbsp;&nbsp;pre_entry_length | 1B       | 00000110<br>更新前一元素的占用大小 |
|                  | &nbsp;&nbsp;&nbsp;&nbsp;encoding         | 2b       | 00                                 |
|                  | &nbsp;&nbsp;&nbsp;&nbsp;length           | 6b       | 000101                             |
|                  | &nbsp;&nbsp;&nbsp;&nbsp;content          | 5B       | Hello                              |
|                  | zlend                                    | 1B       | 11111111                           |


# 遍历
- 通过 `pre_entry_length` 实现向前、向右遍历

# 参考
- [压缩列表](https://redisbook.readthedocs.io/en/latest/compress-datastruct/ziplist.html)
- [定宽整数类型](https://zh.cppreference.com/w/cpp/types/integer)