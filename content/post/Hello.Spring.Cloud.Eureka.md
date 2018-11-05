---
title: "Hello.Spring.Cloud.Eureka"
date: "2018-10-02"
categories:
 - "整理"
tags:
 - "框架"
 - "Spring.Cloud"
toc: true
---


# 细节
## Region-Zone-Eureka
- 名词解析
    - Region
        - 区域
        - 基础服务的地区集合

            |        区域       |      编码      |
            |-------------------|----------------|
            | 亚太-东京         | ap-northeast-1 |
            | 亚太-新加坡       | ap-southeast-1 |
            | 亚太-悉尼         | ap-southeast-2 |
            | 欧洲-爱尔兰       | eu-east-1      |
            | 南美-圣保罗       | sa-east-1      |
            | 美东-北佛杰尼亚   | us-east-1      |
            | 美西-北加利佛尼亚 | us-west-1      |
            | 美西-俄勒风       | us-west-2      |    
    - Zone
        - 可用区/ 机房/ Availability Zone/ AZ
        - 多独立供电、网络服务的数据中心组成
        - 如 `us-west-2a` 区
    - Eureka
- 层级
    - `region= n* zone= n* (m* eureka)`
- 架构
    - ![region-zone-eureka.png](http://doc.yqjdcyy.com/e483c027-70da-4212-9d6d-9314f0107adf.png)

## 模块
- Eureka.Server
    - 存储 `Eureka.Client` 的注册信息
    - 心跳超时，自动注销指定服务节点
        - 默认 **90s**
    - 复制，同步各 `Eureka.Server` 的注册表
- Eureka.Client
    - 服务启动时，通过其向 `Eureka.Server` 端注册服务信息
    - 周期心跳
        - 默认 **30s**
    - 缓存向 `Eureka.Server` 请求的信息
        - 即使 `Eureka.Server` 均宕机，仍可通过缓存信息，找到服务提供者


## 调用
### Robbin+ RestTemplate
- Robbin
    - 基于 HTTP 或 TCP 的客户端`负载均衡`工具

### Feign
- Feign
    - `声明式` HTTP 调用
    - 底层通过 Robbin 进行调用

## 异常
### Eureka.自我保护
- 提示
    - `EMERGENCY! EUREKA MAY BE INCORRECTLY CLAIMING INSTANCES ARE UP WHEN THEY'RE NOT. RENEWALS ARE LESSER THAN THRESHOLD AND HENCE THE INSTANCES ARE NOT BEING EXPIRED JUST TO BE SAFE.`

- 触发机制
    - 在 **15分钟** 内 **超过85%** 的客户端节点都没有正常的心跳

- 效果
    - 不再从注册列表中移除心跳过期的客户端
    - 仍能接受服务的注册、查询，但不可被同步到其它节点
        - 仅当网络稳定时，新注册的信息才会被同步到其它节点

- 配置

    ```yml
    # Eureka.Config.*
    eureka:
        instance:
            # 检测心跳异常的超时时长
            lease-expiration-duration-in-seconds: 90
        server:1
            # 关闭自我保护机构
            enable-self-preservation: false
            # 检查失效服务的间隔时间
            eviction-interval-timer-in-ms: 3000
    ```

# 应用

## Eureka.Server

### Maven

- pom.xml

    ```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>    
    ```

### Properties

- bootstrap.yml

    ```yml
    # Eureka.Config.*
    eureka:
        client:
            # 是否将自身注册至 Eureka.Server
            register-with-eureka: false
            # 是否同步其它 Eureka.Server 节点数据
            fetch-registry: false
        instance:
            # 检测心跳异常的超时时长
            lease-expiration-duration-in-seconds: 90
        server:
            # 关闭自我保护机构
            enable-self-preservation: false
            # 检查失效服务的间隔时间
            eviction-interval-timer-in-ms: 3000
    ```

### Code

- Application.java

    ```java
    // 配置当前服务为 Eureka.Server
    @EnableEurekaServer
    @SpringBootApplication
    public class Application {

        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }
    }    
    ```

### Monitor
- http://localhost:10000/
- ![Eureka-Monitor.png](http://doc.yqjdcyy.com/a66926c7-1fbf-4980-9155-02e81be8065c.png)


## Eureka.Client

### Maven

- pom.xml

    ```xml
		<!--Eureka.Client.*-->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
		</dependency>

        <!-- Eureka.Client.Invoke - Robbin -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-ribbon</artifactId>
		</dependency>

        <!-- Eureka.Client.Invoke - Feign -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-feign</artifactId>
            <!-- **有时加上版本才能正常运行** -->
			<version>1.4.5.RELEASE</version>
		</dependency>
    ```

### Properties

- bootstrap.yml

    ```yml
    ## Eureka.*
    eureka:
        client:
            service-url:
                defaultZone: http://localhost:10000/eureka    
    ```

### Code

- Application.java

    ```java
    @EnableEurekaClient     // 注册为 Eureka 客户端
    @SpringBootApplication
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }
    }
    ```

- ConfigureApplication

    ```java
    @Configuration
    public class ConfigureApplication {

        @Bean               // 注册 RestTemplate 供服务调用
        @LoadBalanced       // 标注为通过负载均衡
        public RestTemplate restTemplate() {
            return new RestTemplate();
        }
    }
    ```

- FeignService.java

    ```java
    // 代理接口类
    // 使用 FeignClient 指定向 Eureka 中注册指定名称的服务
    @FeignClient("portal")
    public interface FeignService {

        // 对应服务内的链接
        @RequestMapping("/config")
        String config();
    }
    ```

- EurekaController.java

    ```java
    @RestController
    @RequestMapping("/eureka")
    public class EurekaController {

        // Ribbon+ RestTemplate
        @Autowired
        RestTemplate restTemplate;

        @RequestMapping(value = "/robbin/{application}/{method}", method = RequestMethod.GET)
        public String invoke(@PathVariable String application, @PathVariable String method) {
            // 调用 portal 应用的 config 接口，接口为 http://PORTAL/config
            // 其中应用名称为 Eureka.Monitor 的 Instances 列表上的 Application 字段
            return restTemplate.getForObject(String.format("http://%s/%s", application.toUpperCase(), method), String.class);
        }

        // Feign
        @Autowired
        FeignService feignService;

        @RequestMapping(value = "/feign/{method}", method = RequestMethod.GET)
        public String feign(@PathVariable String method) {
            return feignService.config();
        }        
    }
    ```


# 参考

## 文档
- Eureka
    - [Spring Cloud第一篇 Eureka简介及原理](http://www.itmuch.com/spring-cloud-1/)
    - [AWS的区域和可用区概念解释](https://blog.csdn.net/awschina/article/details/17639191)
    - [Spring Cloud中，Eureka常见问题总结](http://www.itmuch.com/spring-cloud-sum-eureka/)
    - [Spring Cloud Eureka 自我保护机制](https://www.jianshu.com/p/ee4785a212f6)
    - [Understanding Eureka Peer to Peer Communication](https://github.com/Netflix/eureka/wiki/Understanding-Eureka-Peer-to-Peer-Communication)
    - [F1V3.0-7 Spring Cloud 介绍](https://blog.csdn.net/zhbr_f1/article/details/72301903)
        - Eureka 相关配置参数一览
- Robbin
    - [SpringCloud 教程 | 第二篇: 服务消费者（rest+ribbon）](https://juejin.im/post/5905b43961ff4b0066ba6237)
    - [Spring Cloud之服务调用及使用ribbon实现负载均衡(三)](https://blog.csdn.net/u014401141/article/details/78652101)
    - [Spring Cloud源码分析（二）Ribbon](http://blog.didispace.com/springcloud-sourcecode-ribbon/)
- Feign
    - [Spring Cloud中声明式服务调用Feign](https://blog.csdn.net/u012702547/article/details/78251985)
    - [Spring Cloud Feign 声明式服务调用](https://juejin.im/post/5adee93551882567137dd71f)

## 代码
- [yqjdcyy/Hello_Spring_Cloud](https://github.com/yqjdcyy/Hello_Spring_Cloud/tree/eureka)