+++
date = "2018-05-31T12:10:00+08:00"
title = "Spring.Boot.Publish"
draft = false
tags = ["整理","Spring"]
share = true
+++

[TOC]

# Init
- project.init
- business.finish

# Package
## packaging.war
- pom.xml
	```
	<packaging>war</packaging>
	```

## tomcat.remove
- pom.xml
	```
	<dependency>
	    <groupId>org.springframework.boot</groupId>
	    <artifactId>spring-boot-starter-web</artifactId>
	    <!-- 移除嵌入式tomcat插件 -->
	    <exclusions>
	        <exclusion>
	            <groupId>org.springframework.boot</groupId>
	            <artifactId>spring-boot-starter-tomcat</artifactId>
	        </exclusion>
	    </exclusions>
	</dependency>
	```

## servlet-api.add
- pom.xml
	- `javax.servlet-api`
	```
	<dependency>
	    <groupId>javax.servlet</groupId>
	    <artifactId>javax.servlet-api</artifactId>
	    <version>3.1.0</version>
	    <scope>provided</scope>
	</dependency>
	```
	- OR `tomcat-servlet-api`
	```
	<dependency>
	    <groupId>org.apache.tomcat</groupId>
	    <artifactId>tomcat-servlet-api</artifactId>
	    <version>8.0.36</version>
	    <scope>provided</scope>
	</dependency>
	```

## runner.update
- 与 `Application` 同级
	```
	public class SpringBootStartApplication extends SpringBootServletInitializer {
	    @Override
	    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
	        return builder.sources(Application.class);
	    }
	}
	```

## pack
- `mvn clean package`


# Publish
## conf.update
- update Connector.port 

## aliyun.update
- 更新安全组端口

## webapp.upload

## tomcat.run


# Notice
- spring.boot 项目的发布改造
	- 可正常运行，但服务未正常启动
- tomcat 对外端口为 /conf/server.xml 中 Connector.port
- 服务器防火墙放开 Connector.port
	- centos 新版本中默认使用 Firewalld
- 阿里云实例安全组中，需放开 Connector.port


# Reference
## Spring Boot
- [spring-projects/spring-boot](https://github.com/spring-projects/spring-boot)
- [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)

## Package
- [spring boot项目打包成war并在tomcat上运行的步骤](https://blog.csdn.net/yalishadaa/article/details/70037846)
- [把spring-boot项目部署到tomcat容器中](https://blog.csdn.net/javahighness/article/details/52515226)

## Firewalld
- [Linux.Firewalld](http://domain.yqjdcyy.com/post/linux.firewalld/)

