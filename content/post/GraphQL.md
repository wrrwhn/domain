+++
date = "2017-09-13T22:07:46+08:00"
title = "GraphQL"
draft = false
tags = ["整理","GraphQL"]
share = true
+++

[TOC]

# GraphQL

## 参考
- [GraphQL](http://graphql.org/)
- [Graphql入门](http://www.jianshu.com/p/2ec22fc1219c)
- [GraphQL](http://facebook.github.io/graphql/)
- [GraphQL: A data query language](https://code.facebook.com/posts/1691455094417024/graphql-a-data-query-language/)
- [graphql-go/graphql](https://github.com/graphql-go/graphql)
- [facebook/graphql](https://github.com/facebook/graphql)

## 介绍
### 因果
- 大量并发请求和数据更新的二次请求，造成响应和维护的难度提升
- 不同 app 对相同资源的不同使用方法导致 API 爆炸性增长
- 大而全的通用性接口又不利于移动端使用

### 优点
- 足够描述性的数据抓取通用性 API
- 服务端解释后返回以特定形式


## 机制
### 类型
- Query
    ```
    query {
      hero {
        name
      }
      droid(id: "2000") {
        name
      }
    }
```

- Mutation
```
mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
  createReview(episode: $ep, review: $review) {
    stars
    commentary
  }
}

{
  "ep": "JEDI",
  "review": {
    "stars": 5,
    "commentary": "This is a great movie!"
  }
}
```

### 请求即输出
- 请求格式与返回格式近似
- 任性地满足产品和开发的数据驱动要求

## 等级分层
- 遵循对象层级、关系
    - 服务端需多次网络请求或复杂 SQL 查询
- 促进服务端以层次、图形化结构存储

## 强类型
- 每个层级的查询都对应着特定的类型，而每个类型都描述以一组可用的字段
- 允许 GraphQL 于执行查询前抛出描述性异常信息

## 协议而非存储
- 每个 Graphql 字段均有任意方法支持
- 复杂的排序、存储模型
- 依赖现有的实现，而不指定或提供任何备份存储

## 内省
- 提供强大的平台
- 帮助开发学习和探索 API，而不需要依赖代码库或 CURL 工具

## **版本无关**
- 字段弃用等并不影响数据输出
- 渐进的向后兼容过程消除了版本号的需要


## 实践
- [graphql-go/graphql](https://github.com/graphql-go/graphql)
- [Introduction to GraphQL](http://graphql.org/learn)
