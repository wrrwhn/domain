---
title: "Hello.Spring.Cloud.Zuul"
date: "2018-10-16"
categories:
 - "整理"
tags:
 - "框架"
 - "Spring.Cloud"
toc: true
---

# 作用
- 保障服务无状态
    - 确保对外服务的安全性，增加服务访问的控制控制
    - 保障集群中 REST API 无状态
- 保障接口的复用

# 功能
## 服务路由
- 通过 JVM 实现
## 均衡负载
## 权限控制
- `ZuulFilter`
    - `filterType`
        - pre
            - pre-routing filtering
            - 请求被路由之前调用
        - route
            - routing to an origin
            - 请求路由时调用
        - post
            - post-routing filters
            - 调用后，未调用
        - error
            - error handling
    - `filterOrder`
        - filterOrder 是必须的
        - 但对于非提前执行的过滤器而言是同样的，仅用于指定预先过滤判断的顺序
        - 不需要该顺序值是连续的
    - `shouldFilter`
        - 当返回 `true` 的时候表示执行 `run()` 方法
    - `run`
        - 过滤器实现的**核心**

## 动态路由
## 服务迁移
## 静态响应    

# 实现

## 转跳

### Maven
- `pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
    </dependency>
    ```

### Properties
- `bootstrap.yml`

    ```yml
    zuul:
        routes:
            # v3
            other:
                path: /other/**
                url: http://<ZUUL_HOST>:<ZUUL_PORT>/other-service
            # v2
            portal:
                path: /portal/**
                serviceId: portal
            # v1
            sns: /sns/**
            # v0
            ## hystrix:
            ## 默认通过 `http://<ZUUL_HOST>:<ZUUL_PORT>/<Eureka.ServiceId>/**  ` 访问
    ```

### Code
- `Application`

    ```java
    @EnableEurekaClient
    @SpringBootApplication
    @EnableZuulProxy
    public class Application {

        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }
    }
    ```

### Invoke
- `http://localhost:8302/portal/config`
    - 相当于调用 `http://localhost:8100/config`

## 过滤

### Code
- `ZuulFilter`

    ```java
    public class ZuulFilter extends com.netflix.zuul.ZuulFilter {

        private static Logger logger = LoggerFactory.getLogger(ZuulFilter.class);

        @Override
        public String filterType() {
            return "pre";
        }

        @Override
        public int filterOrder() {
            return 0;
        }

        @Override
        public boolean shouldFilter() {
            return true;
        }

        @Override
        public Object run() throws ZuulException {

            RequestContext ctx = RequestContext.getCurrentContext();
            HttpServletRequest req = ctx.getRequest();

            logger.info("[{}] request to [{}]", req.getMethod(), req.getRequestURL().toString());

            Object token = req.getParameter("token");
            if (null == token) {
                logger.warn("token is empty");
                ctx.setSendZuulResponse(false);
                ctx.setResponseStatusCode(401);
                return null;
            }
            logger.info("token pass");
            return null;
        }
    }
    ```

### Invoke
- `http://localhost:8302/portal/config`
    - `HTTP 401`
- `http://localhost:8302/portal/config?token=yao`
    - `basic`


# 参考

## 官方
- [19. Router and Filter: Zuul](http://cloud.spring.io/spring-cloud-static/Edgware.RELEASE/single/spring-cloud.html#_router_and_filter_zuul)

## 补充
- [Spring Cloud构建微服务架构（五）服务网关](http://blog.didispace.com/springcloud5/)
- [springcloud(十)：服务网关zuul初级篇](http://www.ityouknow.com/springcloud/2017/06/01/gateway-service-zuul.html)

## 代码
- [yqjdcyy/Hello_Spring_Cloud](https://github.com/yqjdcyy/Hello_Spring_Cloud/tree/zuul)