---
title: "Hello.Spring.Cloud"
date: "2018-09-20"
categories:
 - "整理"
tags:
 - "框架"
 - "Spring.Cloud"
toc: true
---


# 架构
## API 调用
- 内容
    - API 调用方
- 类型
    - PC
    - Phone
    - Client

## 服务治理
### 服务注册与发现
- 内容
	- 基于 REST 的服务注册、发布、故障转移
- 类型
	- Netfix Eureka
		- 类型
			- **AP**
		- 作用
			- 服务的注册与发布
		- 功能
			- 注册
			- 发布
			- 熔断
			- 负载
			- 降级
		- 实现
			- Netflix 公司开发
			- C-S 架构
			- 使用客户端连接到服务，并维护心跳连接
		- 推荐
			- 集群保障
	- Spring Cloud Consul
		- 类型
			- **CP**
		- 作用
			- 支持多数据中心的分页式高可用的服务发布和配置共享
		- 功能
			- 健康检测
			- 支持 Http 和 DNS 协议调用 API 
		- 实现
			- HashiCorp 公司开发
			- Go 语言开发
			- 基于 Mozilla Public License 2.0 协议开源
			- 与 Docker 无缝集成
	- Spring Cloud Zookeeper
		- 类型
			- **CP**
		- 作用
			- 为分页式应用提供一致性服务
		- 功能
			- 配置维护
			- 域名服务
			- 分布式同步
			- 组维护
		- 实现
			- Google.Chubby 的开源实现

### 服务调用
#### 调用端熔断器
- 内容
    - 在远程服务不可用时自动熔断，并在远程服务恢复时自动恢复
- 类型
    - Hystrix
        - 优势
            - 避免基础服务故障导致的级联故障
                - 阀值
                    - 20/5s 
                - 打开断路后，服务转到 fallback 并返回固定值
        - 功能
            - 断路器
            - 资源隔离
            - 自我修复
        - 扩展
            - Hystrix Dashboard
                - 实时监控各请求的响应时间、成功率等
            - Turbine
                - 汇总各服务数据

#### 调用端负载均衡
- 内容
    - 提供客户端的负载均衡算法
- 类型
    - Ribbon
        - 优势
            - 易于与 Eureka 等服务发布组件集成
            - 使用 Archaius 完成运行时配置
            - 使用 JMX 暴露去给指标
            - 使用 Servo 发布
            - 多种可插拨的序列化选择
        - 策略
            - 简单轮询
            - 加权响应时间
            - 区域感知轮询
            - 随机负载

#### 调用端代码封装抽象
- 内容
    - 服务调用端代码的抽取和封装
- 类型
    - Feign
        - 功能
            - 可插拨的注解支持
                - Feign 
                - JAX-RS
            - 可插拨的 HTTP 编解码器
            - 支持 Hystrix
            - 支持 Ribbon
            - 支持 Http 请求和响应的压缩

### 服务路由和过滤
- 内容
    - 作为资源的统一访问接口，实行校验、请求等功能
- 类型
    - Zuul
        - 作用
            - 提供动态路由、监控、弹性和安全服务
        - 功能
            - 负载均衡
            - 反射代理
            - 权限认证
            - 动态路由
            - 监控
        - 实现
            - Servlet 2.5
            - JVM 路由
            - 阻塞 API
            - 不支持长连接，如 websockets
            - Filter
    - Gateway
        - 作用
            - 通过横切方式关注案例性、监控、指标和弹性
        - 功能
            - 动态路由
            - 过滤
            - 可修改下游的 Http 的请求和响应
        - 实现
            - Java 8
            - Spring Framework 5
            - Spring Boot 2

### 服务监测
- 内容
    - 监测各端的运行情况
- 类型
    - Spectator
        - 作用
            - 用于收集客户端的相关指标
    - Servo
        - 作用
            - 应用监控库
    - Atlas
        - 作用
            - 存储于内存的，以时间为主线的，多维度数据库

## 分页式链路监控
### 埋点
- 内容
    - 全自动、可配置的数据的埋点
- 类型
    - Spring Cloud Sleuth
        - 作用
            - 日志收集工具包
        - 功能
            - 调用链分析
            - 服务调用关系
            - 请求调用链
            - 调用链各环节的消费时间
        - 实现
            - 封装服务
                - Dapper
                - logbased 追踪
                - Zipkin.HTrace

### 收集
- 内容
    - 埋点数据的收集、存储、统计与展示
- 类型
    - Zipkin
        - 作用
            - 实现数据的收集、存储、查找和展现的分布式追踪系统
        - 存储方式
            - Im-Memory
            - MySql
            - Cassandra
            - Elasticsearch
        - 实现
            - Twitter 公司开源


## 消息组件
- 内容
    - 微服务间的异步通信
    - 功能
        - 发布订阅
        - 分组消费
        - 消息分片
- 类型
    - Spring Cloud Stream
        - 作用
            - 微服务间消息通信的基础框架
        - 功能
            - 封装第三方消息中间信实现
                - Redis
                - RabbitMQ
                - Apache Kafka
        - 实现
            - 基于 Spring Boot 创建
            - 使用 Spring integration 提供与消息代理间的连接
    - Spring Cloud Bus
        - 作用
            - 微服务间的消息通信
        - 实现
            - 基于 Spring Cloud Stream
            - Spring Cloud Stream
        - 场景
            - 结合 Spring Cloud Config 实现动态的配置修改


## 配置中心
- 内容
    - 服务配置的动态管理
- 类型
    - Spring Cloud Config
        - 作用
            - 配置管理
        - 功能
            - 配置存储
            - 接口
        - 来源
            - 本地存储
            - Git
            - Subversion
    - Archaius
        - 作用
            - 配置管理
        - 功能
            - 动态类型化属性
            - 配置操作
                - 线程安全
            - 轮询
            - 回调
        - 原理
            - 每隔固定时间，从配置源读取一次内容
                - 默认配置为 60s


## 安全控制
- 内容
    - 基于 OAUTH2 安全标准的认证和授权
    - 功能
        - 单点登录
        - 资源授权
        - 令牌管理
- 类型
    - Spring Cloud Security
        - 作用
            - 为应用程序添加安全控制
        - 功能
            - OAuth2
        - 实现
            - 对Spring Security的封装

## 集群工具
- 内容
    - 辅助集群服务的调用
- 功能
    - 集群选主
    - 分布式锁
    - 一次性令牌
    - 暂未实现
- 类型
    - Spring Integration
        - 作用
            - 异步消息驱动不同系统间的交互
        - 功能
            - 轻量级的事件驱动交互框架
            - 基于适配器的灵活的系统间交互
    - Spring Cloud Cluster
        - 作用
            - 负载均衡
        - 功能
            - 选举
            - 集群状态一致性
            - 全局锁
            - tokens

## 命令行工具
- 内容
    - 以命令行或脚本方式管理微服务、组件
- 类型
    - Spring Cloud CLI

## 补充
### 大数据
#### Spring Cloud Data Flow
- 作用
    - 针对大量数据的处理、开发的编程模型和托管服务
- 模式
    - ETL
        - Extract-Transform-Load
        - 描述将数据从来源端经过抽取（extract）、交互转换（transform）、加载（load）至目的端的过程
    - 批量运算
    - 持续运算
- 功能
    - 自动部署
- 实现
    - 基于 Spring Boot 的 stream 和 task/batch 重构的流处理和批处理
    - 原生支持
        - Cloud Foundry
        - Apache YARN
        - Apache Mesos
        - Kubernetes

### 定时任务
#### Spring Cloud Task
- 作用
    - 针对短效、定期任务的管理
- 功能
    - 调度实现
        - 完成后自动停止，释放系统占有的资源、线程
        - Quartz 为常驻线程模式
    - 支持多数据库
        - DB2
        - H2
        - HSQLDB
        - MySql
        - Oracle
        - Postgres
        - SqlServer

### 其它
#### Spring Cloud Connectors
- 功能
    - 简化连接到服务的过程
    - 从云平台获取操作
#### Spring Cloud Starters
- 作用
    - Spring Boot 式的启动项目，提供开箱即用的依赖管理
#### Spring Cloud Foundry
- 作用
    - 开源 PaaS 云平台
- 功能
    - 多框架支持
    - 多语言支持
- 实现
    - VMWare 推出


# 概念
## IasS
- 全称
    - Infrastructure-as-a-service
- 描述
    - 基础设施服务
- 内容
    - 计算
    - 存储
    - 网络
- 示例
    - AWS
    - Azure
    - AliYun

## PaaS
- 全称
    - Platform-as-a-service
- 描述
    - 平台服务
- 内容
    - MySql
    - angodb
    - RabbitMQ
    - Java
    - NodeJs
- 示例
    - CloudFoundry
    - OpenShift

## Saas
- 全称
    - Software-as-a-service
- 描述
    - 软件服务
- 内容
    - Email
    - IM
    - Facebook
    - Twitter
- 示例
    - 所有应用

## BBF
- 全称
    - Backend For Frontend
- 作用
    - 作为逻辑层聚合底层服务，针对多端进行展示


# 原理
## CAP
- 全称
    - 布鲁尔定理
- 定义
    - 分布式系统，无法同时满足如下三点
        - Consistency
            - 一致性
            - 所有节点访问同一份最新数据副本
        - Availability
            - 可用性
            - 每次请求均能获取非异常响应
                - 不保证获取的为最新的数据
        - Partition tolerance
            - 分区容错性
                - 始终成立
            - 不同网络分区间通信允许失败


# 参考
## Spring.Cloud
- [Spring Cloud技术分析（序）](http://tech.lede.com/2017/03/15/rd/server/SpringCloud0/)
- [Spring Cloud 项目综述](https://my.oschina.net/geekidentity/blog/876315)
- [网易乐得-Spring Cloud](http://tech.lede.com/tags/Spring-Cloud/)
- [大话Spring Cloud](http://www.ityouknow.com/springcloud/2017/05/01/simple-springcloud.html)
- [Spring Cloud netflix概览和架构设计](http://codin.im/2016/12/15/spring-cloud-architect-intro/)
    - 包含服务的相关异常
- [springcloud(九)：配置中心和消息总线](http://www.ityouknow.com/springcloud/2017/05/26/springcloud-config-eureka-bus.html)
    - `/bus/refresh` 引起 `Eureka` 连接断开
- [微服务架构集大成者—Spring Cloud](https://www.jianshu.com/p/3899d7f47303)
    - 包含各组件的详细介绍
- [史上最简单的 SpringCloud 教程](https://blog.csdn.net/forezp/article/details/70148833)

## Spring.Component
- [Spring Cloud Gateway 入门](https://juejin.im/post/5aa4eacbf265da237a4ca36f)
- [spring cloud task](https://www.jianshu.com/p/fb2e973fb325)
- [Spring Security](http://docs.spring.io/spring-security/site/docs/4.2.2.RELEASE/reference/htmlsingle/)


## 补充
###  CAP
- [CAP 定理的含义](http://www.ruanyifeng.com/blog/2018/07/cap.html)
- [CAP定理](https://zh.wikipedia.org/wiki/CAP%E5%AE%9A%E7%90%86)

### 其它
- [IaaS，PaaS，SaaS 的区别](http://www.ruanyifeng.com/blog/2017/07/iaas-paas-saas.html)
- [BFF —— Backend For Frontend](https://www.jianshu.com/p/eb1875c62ad3)
- [OAuth2（重点），参考文档](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)
- [Spring Security OAuth2，参考文档](http://docs.spring.io/spring-boot/docs/1.5.2.RELEASE/reference/htmlsingle/#boot-features-security-oauth2)

## 整理
- [Spring.Cloud.xmind](http://otzm88f21.bkt.clouddn.com/9b2f1b98-e458-4544-b4a4-b3074ef2a800.xmind)