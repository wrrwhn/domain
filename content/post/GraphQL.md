---
title: "Hello.GraphQL"
date: "2017-09-13"
categories:
 - "整理"
tags:
 - "GraphQL"
toc: true
---

# 简介
## 因果
- 大量并发请求和数据更新的二次请求，造成响应和维护的难度提升
- 不同 app 对相同资源的不同使用方法导致 API 爆炸性增长
- 大而全的通用性接口又不利于移动端使用

## 优点
- 足够描述性的数据抓取通用性 API
- 服务端解释后返回以特定形式

## 类型
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

## 请求即输出
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


# 教程

## 查询&变更
### 请求

  ```
  # 指定 **操作类型**[query|mutation|subscription]、**操作名称**(明确操作的意义、便于排查)
  query UserSystem{
    # 参数传替 & 变量调用
    human(id: $id) {
      name
      # 支持多级选择
      ## 标量以枚举 `CUSTOM` 作为参数，实现服务端转换
      ## 构建组件，通过参数控制
      friends(type: CUSTOM)  @include(if: $withFriends) {
        name
      },

      # 别名
      site: human(id: "2"){
        # 无字段，获取该位置对象类型的名称
        __typename
        name
      },

      # 重复片段
      admins: human(type: ADMIN){
        ...complexFields
      },
      users: human(type: USER){
        ...complexFields
      },
    }
  }

  # 变量定义
  ## v1
  variables{
    "id": 1,
    "withFriends": true
  }

  ## v2
  ### 声明类型值可为 标量| 枚举| Schema.Object
  ### ! 表示是否是可选项
  ### = 指定默认值
  query UserSystem($id: [声明类型][!]= "默认值")

  # 片段定义
  ## v1
  fragment complexFields on human{
    id
    name
  }
  ## v2
  ### 内联片段，仅在返回类型是 `human` 情况下执行
  ... on human{
    id
  }

  # 指令
  ## @include(if: Boolean)  仅当为 true 时包含
  ## @skip(if: Boolean)     仅当为 true 跳过
  ```

### 输出

  ```
  {
    "data":{

      # 多级
      "human":{
        "name": "R2-D2",
        "friends": [
          {"name": "Luke"},
          {"name": "Han"},
        ]
      },

      # 别名
      "site":{
        "__typename": "human",
        "name": "绝地神殿"
      },

      # 片段
      "admins":[
        {
          "id": 1,
          "name": R2-D2",
        }
      ],
      "users":[
        {
          "id": 2,
          "name": Han",
        }
      ],
    }
  }
  ```


## Schema

### 类型
- 对象
  - GraphQL.newObject

    ```
    type Character {
      # 非空文本
      name: String!
      # 非空数组
      appearsIn: [Episode]!
    }
    ```

- 参数

  ```
  type User{
    # 当 unit 参数未传值时，将其默认为 `METER`
    momey(unit: LengthUnit = METER): Float
  }
  ```

- 标量
  - 对应 GraphQL 查询的子节点,系统最小颗粒
  - 系统默认标题
    - Int
      - 有符号 32 位整数。
    - Float
      - 有符号双精度浮点值。
    - String
      - UTF‐8 字符序列。
    - Boolean
      - true 或者 false。
    - ID
      - ID 标量类型表示一个唯一标识符，通常用以重新获取对象或者作为缓存中的键，不可人为阅读类型
  - 自定义标量 `Date`

    ```
    scalar Date
    ```

- 枚举
  - 可在 `Schema` 中任意处使用
  - 定义

    ```
    enum Role{
      ADMIN
      USER
    }
    ```

- 列表

  ```
  human{
    # 数组本身可为空，但内容值不能为空
    name: [String!]
    # 数组不能为空，但内容值可为空
    phone: [String]!
  }
  ```

- 接口

  ```
  interface Character{
    id: ID!
    name: String!
  }

  # 可结合内联片段实现调用
  type Human implements Character{
    id: ID!
    name: String!
    friends: [Character]
  }
  ```

- 联合类型
  - 不指定类型之前的共同字段，只表示返回联合类型的地方，可能得到其定义中的任一类型对象

  ```
  union SearchResult = Human | Droid | Starship
  ```

- 输入类型
  - 定义修改时的所需的复杂输入对象

    ```
    mutation CreateInput($obj: InputObj){
      create(obj: $obj){
        id
        name
      }
    }

    input InputObj{
      id: Int!
      Name: String!
    }

    variables{
      "obj"{
        "id": 1,
        "name": 10
      }
    }
    ```


# 参考
## 官方
- [GraphQL 入门](http://graphql.cn/learn/)
- [GraphQL](http://graphql.cn/)
- [GraphQL: A data query language](https://code.facebook.com/posts/1691455094417024/graphql-a-data-query-language/)
- [facebook/graphql](https://github.com/facebook/graphql)

## 补充
- [Graphql入门](http://www.jianshu.com/p/2ec22fc1219c)
- [30分钟理解GraphQL核心概念](https://segmentfault.com/a/1190000014131950)
- []()

## 框架
- [GraphQL](http://facebook.github.io/graphql/)
- [graphql-go/graphql](https://github.com/graphql-go/graphql)
- [Introduction to GraphQL](http://graphql.org/learn)