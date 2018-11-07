---
title: "Hello.Spring.Cloud.Config"
date: "2018-09-30"
categories:
 - "整理"
tags:
 - "框架"
 - "Spring.Cloud"
toc: true
---

# 组件
## 配置读取
- Spring.Cloud.Config.Server
- Spring.Cloud.Config.Client

## 主动更新
- Spring.Cloud.Config.Server
- Spring.Cloud.Config.Client
- Spring.Cloud.Bus
- Redis/ RabbitMQ/ Kafka

# 实现

## Config-Server

### Maven
- pom.xml

    ```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-config-server</artifactId>
    </dependency>
    ```

### Properties

- src/main/resource/application.yml

    ```yml
    ## config.server git
    #  cloud:
    #    config:
    #      server:
    #        git:
    #          uri: https://github.com/yqjdcyy/Hello_Spring_Cloud_Config

    ## config.server locale
    profiles:
        active: native
    cloud:
        config:
        server:
            native:
            search-locations: D:/work/git/yao/java/Hello_Spring_Cloud_Config
    ```

### Code

- Application.java

    ```java
    @SpringBootApplication
    @EnableConfigServer     // * 必须项
    public class Application {
        public static void main(String[] args) {SpringApplication.run(Application.class, args);}
    }
    ```

### Request
- 格式
    - `/{application}/{env}(/{label})`
    - `(/{label})/{application}-{env}.[yml|properties]`
        - label
            - git 上对应的分支，**可跳过**，默认为 `master`
        - application
            - 应用名称，于应用配置文件中的 `spring.application.name` 来指定
        - env
            - 自定义的环境，如 `dev` `test` `prod`
            - 默认配置，如 `default`

- 示例
    ```json
    # 请求
    ## Format.Path
    http://localhost:10086/portal/dev
    http://localhost:10086/portal/default
        = http://localhost:10086/portal/default/master
    
    ## Format.File
    http://localhost:10086/portal-dev.yml
    http://localhost:10086/portal-default.yml
        = http://localhost:10086/portal.yml
        = http://localhost:10086/master/portal.yml

    # 返回值
    {
        "name": "portal",
        "profiles": [
            "default"
        ],
        "label": null,
        "version": "58e079a09fc0e4f58a51523ad4f997b9262aaa2a",
        "state": null,
        "propertySources": [
            {
                "name": "https://github.com/yqjdcyy/Hello_Spring_Cloud_Config/portal.yml",
                "source": {
                    "config.hello": "basic-v2"
                }
            },
            {
                "name": "https://github.com/yqjdcyy/Hello_Spring_Cloud_Config/application.yml",
                "source": {
                    "config.env": "basic"
                }
            }
        ]
    }
    ```


## Config-Client

### Maven

- pom.xml

    ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>    
    ```

### Properties

- src/main/resource/bootstrap.yml

    ```yml
    spring:
        cloud:
            config:
                uri: http://localhost:10086    
    ```

### Code

- ConfigController

    ```java
    @RestController
    @RequestMapping("/config")
    public class ConfigController {
        @Value("${config.hello:undefined}")     // 读取配置文件，无此项时以 `undefined` 代替
        private String hello;
    }
    ```


## Client.Refresh

### Maven

- pom.xml

    ```xml
    <!-- 自动添加 health/ info 等接口 -->
    <!-- 使用的为 2.0.5.RELEASE 版本，接口已由 /* 变更为 /actuator/* -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>    
    ```

### Properties

- aaplication.yml
    ```yml
    ## Actuator.*
    management:
        endpoints:
            web:
                exposure:
                    # 默认仅开放 ["health", "info"] 接口，调整为 “*" 则开放所有接口
                    include: "*"
    ```

### Code

- ConfigController

    ```java
    // 添加 RefreshScope 注解，以便调用 `/refresh` 接口后，自动将更新值更新到该类
    @RefreshScope
    @RestController
    @RequestMapping("/config")
    public class ConfigController{...}
    ```

### Request
- `GET  /actuator`
    - 查询 `actuator` 开放的所有接口
- `POST  /actuator/refresh`
    - 指定当前服务 **获取最新配置**
- `GET  /actuator/health`
    - 查询程序运行状态


## Bus.Refresh

### Maven

- `*` /pom.xml

    ```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-bus-kafka</artifactId>
    </dependency>
    ```

### Properties

- config-server/application.yml

    ```yml
    ## Bus.Kafka
    spring:
        stream:
            kafka:
                binder:
                    brokers: "192.168.16.37:9092"

    ## Actuator.*
    ## 将 /bus/refresh 接口开放出来， 最新版本中路径更新为 /actuator/bus-refresh
    management:
        endpoints:
            web:
                exposure:
                    include: "*"
    ```

- portal/application.yml

    ```yml
    ## Bus.Kafka
    spring:
        stream:
            kafka:
                binder:
                    brokers: "192.168.16.37:9092"
    ```

### Log
- stdout.log
    ```log
    partitions assigned: [springCloudBus-0]
    ```

### Request
- `GET      /actuator`
    - 查看开放的所有监控接口
- `POST     /actuator/bus-refresh`
    - 所有关联应用重新拉取配置
- `POST     /actuator/bus-refresh/portal`
    - 指定 `portal` 应用进行配置拉取

## 细节

### 配置格式
- 键值对
- YAML 格式

### 来源
- Git
- SVN
- 本地目录
- JDBC
- 覆盖
    - `spring.cloud.config.server.overrides`

### 拉取
- git 更新后，服务端 **自动拉取最新** 配置
- 通过 `spring.application.name` 的属性值，作为注册到 Config Server 的客户端 `portal` ID
- 默认推送 `application.[properties|yml]` 文件
- 而 `portal.[properties|yml]` 将覆盖 `application.[properties|yml]` 中的相应配置
    - 示例

        ``` yaml
        # 文件中的相应属性逐级覆盖
        application.yml
        portal.yml
        portal-prod.yml
        ```

### 正则
- 配置文件匹配

    ```yaml
    spring:
    cloud:
        config:
        server:
            git:
            uri: https://github.com/spring-cloud-samples/config-repo
            repos:
                development:
                pattern:
                    - '*/development'
                    - '*/staging'
                uri: https://github.com/development/config-repo
                staging:
                pattern:
                    - '*/qa'
                    - '*/production'
                uri: https://github.com/staging/config-repo    
    ```


# 参考
## 文档
- [Part II. Spring Cloud Config](http://cloud.spring.io/spring-cloud-static/Edgware.RELEASE/single/spring-cloud.html#_spring_cloud_config)
- [微服务之分布式配置中心Cloud Config](http://blueskykong.com/2017/11/20/config-server1/)
- [spring cloud 学习(5) - config server](https://www.cnblogs.com/yjmyzz/p/spring-cloud-config-server-tutorial.html)
- [spring boot 2 使用 actuator 404的问题](https://my.oschina.net/iyinghui/blog/1835220)

## 代码
- [yqjdcyy/Hello_Spring_Cloud](https://github.com/yqjdcyy/Hello_Spring_Cloud)