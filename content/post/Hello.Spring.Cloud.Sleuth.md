---
title: "Hello.Spring.Cloud.Sleuth"
date: "2018-10-16"
categories:
 - "整理"
tags:
 - "框架"
 - "Spring.Cloud"
toc: true
---


# 功能
- 链路追踪
    - 查看该请求经过了哪些服务，当前哪些服务异常（**实时数据**）
- 可视化异常
    - 在 `zipkin` 界面上查看异常情况
- 分析蚝时
    - 记录各采样请求的蚝时，可控地进行服务扩容
- 优化链路
    - 根据服务调用的频次，合理配置各服务数量(**全量数据**)

# 结构
- 采集端
    - sleuth
- 显示端
    - zipkin
        - 数据存储
            - In-Memory
            - Kafka
            - MySql
            - Elasticsearch
            - Cassandra
- 连接方式
    - http
    - stream
        - kafka
        - rabbitmq.binder

# 原理
## 由 Google.dapper 论文发展而来

## 示例
- ![tracing.png](http://otzm88f21.bkt.clouddn.com/f4bfe281-f05f-4f1a-aa49-c6ae795a1d5c.png)
- ![sleuth-struct.png](http://otzm88f21.bkt.clouddn.com/f95c6cdd-29f2-4b03-8d66-065e47b6b9f9.png)
- ![tracing-example.png](http://otzm88f21.bkt.clouddn.com/2894cbc9-1502-4c4c-a5d4-d60cd983e2b0.png)

## 术语

| 模块  | 字段    | 含义                                                                                    |
|-------|---------|-----------------------------------------------------------------------------------------|
| trace |         | 多个 `span` 组成的树状请求                                                              |
| span  |         | 工作单元，以 `spanId` 标识<br>服务间调用时生成同一 `spanId` 的客户端请求和服务端处理记录 |
|       | traceId | 此次请求的标识 ID                                                                       |
|       | pspanId | 上一服务请求 `spanId`                                                                   |
|       | spanId  | 此次请求的 `spanId`                                                                     |
|       | cs      | client-send                                                                             |
|       | cr      | client-receive                                                                          |
|       | ss      | server-send                                                                             |
|       | sr      | server-receive                                                                          |

## 采样
- 配置
    - `spring.sleuth.sampler.percentage=0.1`
- 默认
    - 采样率为 `0.1`
- 算法
    - Reservoir sampling | 水塘抽样
- 实现
    - `PercentageBasedSampler`

# 细节
## Sleuth
### Http
- `TraceFilter`
    - 对请求添加 `X-B3-SpanId`、 `X-B3-TraceId`等属性进行链路跟踪
- `TraceHandlerInterceptor`
    - 检测到 `HttpServletRequest` 中若不存在

# 实现

### Maven
- `Server.pom.xml`

    ```xml
    <dependency>
        <groupId>io.zipkin.java</groupId>
        <artifactId>zipkin-server</artifactId>
        <version>2.11.7</version>
    </dependency>
    <dependency>
        <groupId>io.zipkin.java</groupId>
        <artifactId>zipkin-autoconfigure-ui</artifactId>
        <version>2.11.7</version>
    </dependency>
    ```

- `Client.pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-zipkin</artifactId>
    </dependency>
    ```

### Properties
- `Server.bootstrap.yml`

    ```yml
    # 未配置，默认走 In-Memory 形式
    management:
        metrics:
            web:
                server:
                    # 修复运行后无法正常运行的异常
                    auto-time-requests: false
    ```
- `Client.bootstrap.yml`

    ```yml
    spring:
        sleuth:
            sampler:
                # 采集率调整为 100%，全部采集
                probability: 1.0
        zipkin:
            # 指定 Zipkin 的推送地址
            base-url: http://localhost:8303
    ```

### Code
- `Server.Application`

    ```java
    // v2.* 版本请引用此路径下文件
    import zipkin2.server.internal.EnableZipkinServer;

    @EnableEurekaClient
    @SpringBootApplication
    // 启动 Zipkin 服务收集
    @EnableZipkinServer
    public class Application {

        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }
    }
    ```

- `Client`

    ```java
    // 无须添加代码！
    ```

### Invoke
- `http://localhost:8303/zipkin/index.html`
    - Zipkin 首页

- `http://localhost:8303/zipkin/`
    - Zipkin 搜索
    - 注： 如若仅使用 `Feign` 或 `Robbin` 进行转跳，不会判定为 `depth` 增加


# 补充
## ZipKin
- Twitter 开源的分布式跟踪系统
- 致力于收集服务的定时数据，用以解决微服务架构中的延迟问题
- 功能
    - 收集
    - 存储
        - In-Memory
            - 测试推荐
        - Kafka
        - MySql
        - Elasticsearch
            - **生产推荐**
        - Cassandra
    - 查找
    - 展现
        - 提供 Web 前端界面进行数据展示

## Error
- `Caused by: java.lang.ClassNotFoundException: org.springframework.boot.actuate.metrics.buffer.CounterBuffers`
    - 配置
        - Spring.Boot 2.0.5.RELEASE
        - Zipkin.Server 1.30.2
        - Zipkin.UI 1.30.2
    - 参考
        -[I get a class error when trying to create a reactive spring cloud sleuth zipkin server](https://github.com/spring-cloud/spring-cloud-sleuth/issues/736)
        
            ```
            Yeah. See #727. Short summary: Zipkin doesn't work with Boot 2.0, so if you need a stream server, use Spring Boot 1.5.
            ```
- `java.lang.IllegalArgumentException: Prometheus requires that all meters with the same name have the same set of tag keys.`
    - 参考
        - [zipkin日志收集注意事项](https://blog.csdn.net/duanqing_song/article/details/80422301)

            ```
            a、将management.metrics.web.server.auto-time-requests=false设置为false,默认为true;
            b、重写DefaultWebMvcTagsProvider或者实现接口WebMvcTagsProvider，参照DefaultWebMvcTagsProvider的写法，只需要把DefaultWebMvcTagsProvider下getTages()方法的WebMvcTags.exception(exception)去除掉。
            ```

# 参考
## 官方
-[Part VII. Spring Cloud Sleuth](http://cloud.spring.io/spring-cloud-static/Edgware.RELEASE/single/spring-cloud.html#_spring_cloud_sleuth)
-[Dapper, a Large-Scale Distributed Systems Tracing Infrastructure](https://ai.google/research/pubs/pub36356)

## 补充
-[Spring Cloud技术分析（3）- spring cloud sleuth](http://tech.lede.com/2017/04/19/rd/server/SpringCloudSleuth/)
-[springcloud(十二)：使用Spring Cloud Sleuth和Zipkin进行分布式链路跟踪](http://www.ityouknow.com/springcloud/2018/02/02/spring-cloud-sleuth-zipkin.html)
-[Spring Cloud Sleuth进阶实战](https://blog.csdn.net/forezp/article/details/76795269)
-[Dapper，大规模分布式系统的跟踪系统](https://bigbully.github.io/Dapper-translation/)
-[]()

## 代码
-[]()
-[]()
-[]()
-[]()
