---
title: "Redis.数据结构.SDS"
date: "2018-11-25"
categories:
 - "整理"
tags:
 - "Redis"
 - "数据结构"
toc: true
---

# 描述
- 解析
    - Simple Dynamic String
    - 简单动态字符串

- 作用
    - 实现字符串对象（StringObject）
    - **内部用于替换 `char*`**
        - 支持**长度**计算
            - 计算复杂度为 `θ(N)`
        - 支持**追加**操作
            - 每次追加均需要进行**内存**的重新**分配**
        - 确保**二进制安全**
            - C语言中，字符串可用 `\0` 的 `char` 数组表示
                - 例如 `hello\0`
            - 导致无法正确地存储和读取 `hell\0999\0` 形式的数据

# 定义

| 类     | 属性       | 作用                                                         |
|--------|------------|--------------------------------------------------------------|
| sdshdr |            |                                                              |
|        | int len    | 字符长度                                                     |
|        | int free   | 字符数组剩余长度                                             |
|        | char buf[] | 字符数组，存放字符串<br>实际长度= len+1，多出来的用于存储 '\0' |


# 追加

## 内存分配逻辑
- 追加超出范围后，为 buf 重新分配内存
    - newlen= sdshdr.len+ append.len
    - 判断 `newlen< SDS_MAX_PREALLOC`
        - 符合时，`newlen*= 2`
        - 不符合时，`newlen+= SDS_MAX_PREALLOC`
    - `SDS_MAX_PREALLOC` 默认为 `1024* 1024`= `1MB`
    - `buf.size= newlen+ 1`
        - 多出来的一位，用于存储 `\0` 标志位
- 优势在于减少内存分配的次数
- 缺陷在于多占用内存，而且不会主动释放        

## 示例

| 阶段          | 属性  | 数值                       |
|---------------|-------|----------------------------|
| 设置 "hello"  |       |                            |
|               | len   | 5                          |
|               | free  | 0                          |
|               | buf[] | "hello\0"                  |
| 追加 " world" |       |                            |
|               | len   | 11                         |
|               | free  | 11                         |
|               | buf[] | "hello workd\0           " |


# 参考
- [简单动态字符串](https://redisbook.readthedocs.io/en/latest/internal-datastruct/sds.html)
- [分布式缓存Redis之二进制安全](https://blog.csdn.net/u011489043/article/details/78738374)