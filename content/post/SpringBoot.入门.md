---
title: "Hello.SpringBoot"
date: "2016-12-11"
categories:
 - "整理"
tags:
 - "Spring"
toc: true
---


# 作用
    创建和启动新的 Spring 框架项目
# 特性
    嵌入 Tomcat 或 Jetty
    提供生产环境使用功能，如性能指标、应用信息和应用健康检查
# POM 配置
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>com.midgetontoes</groupId>
<artifactId>spring-boot-simple</artifactId>
<version>1.0-SNAPSHOT</version>
<properties>
 <spring.boot.version>1.1.4.RELEASE</spring.boot.version>
</properties>
<dependencies>
 <dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-web</artifactId>
 <version>${spring.boot.version}</version>
 </dependency>
</dependencies>
<build>
 <plugins>
 <plugin>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-maven-plugin</artifactId>
<version>${spring.boot.version}</version>
 <executions>
 <execution>
 <goals>
 <goal>repackage</goal>
 </goals>
 </execution>
 </executions>
 </plugin>
 </plugins>
</build>
</project>
```
# 启动程序
```
@RestController
@EnableAutoConfiguration
public class Application {
 @RequestMapping("/")
 String home() {
 return "Hello World!";
 }
 public static void main(String[] args) throws Exception {
 SpringApplication.run(Application.class, args);
 }
}
```
# 推荐基础 POM 文件
- spring-boot-starter    核心 POM，包含自动配置支持、日志库和对 YAML 配置文件的支持。
- spring-boot-starter-amqp    通过 spring-rabbit 支持 AMQP。
- spring-boot-starter-aop    包含 spring-aop 和 AspectJ 来支持面向切面编程（AOP）。
- spring-boot-starter-batch    支持 Spring Batch，包含 HSQLDB。
- spring-boot-starter-data-jpa    包含 spring-data-jpa、spring-orm 和 Hibernate 来支持 JPA。
- spring-boot-starter-data-mongodb    包含 spring-data-mongodb 来支持 MongoDB。
- spring-boot-starter-data-rest    通过 spring-data-rest-webmvc 支持以 REST 方式暴露 Spring Data 仓库。
- spring-boot-starter-jdbc    支持使用 JDBC 访问数据库。
- spring-boot-starter-security    包含 spring-security。
- spring-boot-starter-test    包含常用的测试所需的依赖，如 JUnit、Hamcrest、Mockito 和 spring-test 等。
- spring-boot-starter-velocity    支持使用 Velocity 作为模板引擎。
- spring-boot-starter-web    支持 Web 应用开发，包含 Tomcat 和 spring-mvc。
- spring-boot-starter-websocket    支持使用 Tomcat 开发 WebSocket 应用。
- spring-boot-starter-ws    支持 Spring Web Services。
- spring-boot-starter-actuator    添加适用于生产环境的功能，如性能指标和监测等功能。
- spring-boot-starter-remote-shell    添加远程 SSH 支持。
- spring-boot-starter-jetty    使用 Jetty 而不是默认的 Tomcat 作为应用服务器。
- spring-boot-starter-log4j    添加 Log4j 的支持。
- spring-boot-starter-logging    使用 Spring Boot 默认的日志框架 Logback。
- spring-boot-starter-tomcat    使用 Spring Boot 默认的 Tomcat 作为应用服务器
# 外部配置（高到低）
- 命令行参数。
- 通过 System.getProperties() 获取的 Java 系统参数。
- 操作系统环境变量。
- 从 java:comp/env 得到的 JNDI 属性。
- 通过 RandomValuePropertySource 生成的“random.*”属性
     - user.count=${random.int}
    - user.max=${random.long}
     - user.number=${random.int(100)}
- 应用 Jar 文件之外的属性文件。
- 应用 Jar 文件内部的属性文件。
- 在应用配置 Java 类（包含“@Configuration”注解的 Java 类）中通过“@PropertySource”注解声明的属性文件。
- 通过“SpringApplication.setDefaultProperties”声明的默认属性。
# 属性文件
### 搜索位置， 由高到低
- 当前目录的“/config”子目录。
- 当前目录。
- classpath 中的“/config”包。
- classpath
### 配置属性
- spring.config.name：配置属性指定不同的属性文件名称
- spring.config.location：添加额外属性文件搜索路径
- 配置多个 profile ：文件名格式为 application-{profile}
# 运维支持
### 条件
    POM 文件中添加 org.springframe.boot:spring-boot-starter-actuator 依赖
### HTTP 服务：
- autoconfig    显示 Spring Boot 自动配置的信息。    是
- beans    显示应用中包含的 Spring bean 的信息。    是
- configprops    显示应用中的配置参数的实际值。    是
- dump    生成一个 thread dump。    是
- env    显示从 ConfigurableEnvironment 得到的环境配置信息。    是
- health    显示应用的健康状态信息。    否
- info    显示应用的基本信息。    否
- metrics    显示应用的性能指标。    是
- mappings    显示 Spring MVC 应用中通过“
- @RequestMapping”添加的路径映射。    是
- shutdown    关闭应用。    是
- trace    显示应用相关的跟踪（trace）信息。    是
### 例子：
#### Health 服务
##### 作用：
    对应用本身、关系数据库连接、MongoDB、Redis 和 Rabbit MQ 的健康状态的检测
##### 前置条件：
    实现 org.springframework.boot.actuate.health.HealthIndicator 接口
    返回一个 org.springframework.boot.actuate.health.Health 对象
##### 实现类：
```
    @Component
    public class AppHealthIndicator implements HealthIndicator {
     @Override
     public Health health() {
     return Health.up().build();
     }
    }
```   
##### 返回结果：
{"status":"UP","app":{"status":"UP"},"db":{"status":"UP","database":"HSQL Database Engine","hello":1}}
# JMX 管理
    - Spring Boot 默认提供 JMX 管理的支持
    - 注解支持：@ManagedResource、@ManagedAttribute 和 @ManagedOperation
    - MBean 域：org.springframework.boot
