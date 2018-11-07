---
title: "Hello.Servlet"
date: "2018-11-07"
categories:
 - "整理"
tags:
 - "java"
toc: true
---


# 整体
## 结构图
- ![Servlet.jpg](http://doc.yqjdcyy.com/8c2fa19c-349c-4f95-956d-082fc70d3243.jpg)

## 调用顺序
### Servlet
- Filter.Pre
- Servlet.service
- Filter.After

### Controller
- Filter.Pre
- Interceptor.Pre
- HandlerAdapter
- Interceptor.Post
- Interceptor.Complete
- Filter.After

# 细节
## Servlet
### 说明
- 通过 `Servlet` 规范套接字请求，声明了初始化、销毁的生命周期和处理请求的输入输出规范
    - 配置以分发处理指定响应路径的请求，如专门处理 `/resource` 的资源相关业务
    - 具体实现可通过实现 `HttpServlet` 来支持
    - 屏蔽了不同容器的差异性，保障了代码利用

### 代码
#### Maven
- `pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ```

#### Code
- `HelloServlet.java`

    ```java
    // 指定响应分发路径
    @WebServlet(urlPatterns = "/hello", description = "Hello.Servlet")
    public class HelloServlet extends HttpServlet {

        // 业务处理
        @Override
        public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {

            // 简化实现，未支持请求方法差异化处理
            PrintWriter writer = res.getWriter();
            writer.print("Hello");
            writer.flush();
            writer.close();
        }
    }
    ```

- `Application.java`

    ```java
    @SpringBootApplication
    @ServletComponentScan
    public class Application {

        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }
    }    
    ```



## Filter
### 说明
- 于 `Servlet` 调用前后，进行业务拦截处理
    - 各请求均需要经过 `Filter.Chain` 中的每个过滤器
    - 未指定顺序情况下，将按照名称倒序执行
    - 调用 `chain.doFilter(request, response)` 继续执行

- 常用内容
    - 用户授权
    - 日志
    - 编解码
    - 事务

### 代码
#### Maven
- `pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ```

#### Code
- `Application`

    ```java
    @SpringBootApplication
    @ServletComponentScan("com.yao.filter")
    public class Application {

        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }
    }
    ```

- `LogFilter`

    ```java
    // 拦截所有请求
    @WebFilter(urlPatterns = "/*", filterName = "filter.log")
    public class LogFilter implements Filter {

        @Override
        public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {

            // 请求前处理
            long start = System.currentTimeMillis();
            chain.doFilter(request, response);
            // 请求后处理
            System.out.printf("Request[%s].cost= %d\n", ((HttpServletRequest) request).getRequestURL(), System.currentTimeMillis() - start);
        }
    }    
    ```


## Listener
### 作用
- 针对 `application`/ `session`/ `request` 三者的创建、销毁的监听
- 场景
    - 加载配置信息
    - 获取在线人数
    - 请求日志

### 代码
#### Maven
- `pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ```

#### Code
- `Application`

    ```java
    @SpringBootApplication
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }
    }    
    ```

- `ListenerConfig`

    ```java
    @Configuration
    public class ListenerConfig {

        @Bean
        public ServletListenerRegistrationBean<ORServletContextListener> initServletContextListener() {

            ServletListenerRegistrationBean<ORServletContextListener> registration = new ServletListenerRegistrationBean<ORServletContextListener>();
            registration.setListener(new ORServletContextListener());
            return registration;
        }

        @Bean
        public ServletListenerRegistrationBean<ORSessionListener> initSessionListener() {

            ServletListenerRegistrationBean<ORSessionListener> registration = new ServletListenerRegistrationBean<ORSessionListener>();
            registration.setListener(new ORSessionListener());
            return registration;
        }

        @Bean
        public ServletListenerRegistrationBean<ORRequestListener> initRequestListener() {

            ServletListenerRegistrationBean<ORRequestListener> registration = new ServletListenerRegistrationBean<ORRequestListener>();
            registration.setListener(new ORRequestListener());
            return registration;
        }
    }    
    ```

- `ORRequestListener`

    ```java
    public class ORRequestListener implements ServletRequestListener {

        @Override
        public void requestInitialized(ServletRequestEvent sre) {
            System.out.println("\tOverride.ServletRequest.Listener.init");
        }
        @Override
        public void requestDestroyed(ServletRequestEvent sre) {
            System.out.println("\tOverride.ServletRequest.Listener.destroy");
        }
    }
    ```

- `ORServletContextListener`

    ```java
    public class ORServletContextListener implements ServletContextListener{

        @Override
        public void contextInitialized(ServletContextEvent sce) {
            System.out.println("\tOverride.ServletContext.Listener.init");
        }

        @Override
        public void contextDestroyed(ServletContextEvent sce) {
            System.out.println("\tOverride.ServletContext.Listener.destroy");
        }
    }
    ```

- `ORSessionListener`

    ```java
    public class ORSessionListener implements HttpSessionListener {

        public static final String SESSION_LIVE_COUNT = "SESSION-LIVE-COUNT";


        @Override
        public void sessionCreated(HttpSessionEvent se) {

            System.out.println("\tOverride.HttpSession.Listener.init");

            Object obj = se.getSession().getAttribute(SESSION_LIVE_COUNT);
            se.getSession().setAttribute(SESSION_LIVE_COUNT, null == obj ? 1 : Integer.valueOf(obj.toString()) + 1);
        }

        @Override
        public void sessionDestroyed(HttpSessionEvent se) {

            System.out.println("\tOverride.HttpSession.Listener.destroy");

            Object obj = se.getSession().getAttribute(SESSION_LIVE_COUNT);
            se.getSession().setAttribute(SESSION_LIVE_COUNT, null == obj ? 0 : Integer.valueOf(obj.toString()) - 1);
        }
    }
    ```

- `UserController`

    ```java
    public class UserController {

        private final String USER_SESSION_KEY = "SESSION:USER";


        @RequestMapping(value = {"", "/"}, method = RequestMethod.GET)
        public List<String> list() {

            return Stream.of("admin", "user").collect(Collectors.toList());
        }

        @RequestMapping(value = "/{id}", method = RequestMethod.GET)
        public UserVo get(@PathVariable Long id, HttpServletRequest request) {

            request.getSession().setAttribute(USER_SESSION_KEY, id);
            return new UserVo(id, "admin");
        }

        @RequestMapping(value = "/{id}", method = RequestMethod.DELETE)
        public void delete(@PathVariable Long id, HttpServletRequest request) {

            request.getSession().removeAttribute(USER_SESSION_KEY);
        }

        @RequestMapping(value = "/live", method = RequestMethod.GET)
        public Integer live(HttpServletRequest request) {

            Object obj = request.getSession().getAttribute(ORSessionListener.SESSION_LIVE_COUNT);
            return null == obj ? 0 : Integer.parseInt(obj.toString());
        }
    }
    ```

#### Invoke

-  `application.start`

    ```java
    	Override.ServletContext.Listener.init
    ```

- `/user    GET`

    ```java
        Override.ServletRequest.Listener.init
    2018-11-06 15:57:22.289  INFO 221864 --- [nio-8080-exec-4] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
    2018-11-06 15:57:22.289  INFO 221864 --- [nio-8080-exec-4] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
    2018-11-06 15:57:22.373  INFO 221864 --- [nio-8080-exec-4] o.s.web.servlet.DispatcherServlet        : Completed initialization in 84 ms
        Override.ServletRequest.Listener.destroy
    ```

- `/user/1  GET`

    ```java
        Override.ServletRequest.Listener.init
        Override.HttpSession.Listener.init
        Override.ServletRequest.Listener.destroy
    ```

- `/user/1  DELETE`

    ```java
    	Override.ServletRequest.Listener.init
	    Override.ServletRequest.Listener.destroy
    ```

- `/user/live   GET`


## Interceptor

### 说明
- 基于面向切面编程,使用 java 反射机制实现
- 仅过 `Controller` 中的 `HTTP` 请求进行拦截
- 可针对响应前、响应后和完成渲染三个时机进行控制
    - 通过抛出异常来阻断执行
- 多个拦截器，依据声明的顺序执行
    - **正序** 执行 `preHandle`
    - **倒序** 执行 `postHandle`
    - **倒序** 执行 `afterCompletion`

### 代码
#### Maven
- `pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ```

#### Code
- `Application`

    ```java
    @SpringBootApplication
    public class Application {

        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }
    }    
    ```

- `LogInterceptor`

    ```java
    public class LogInterceptor implements HandlerInterceptor {

        private final String LOG_KEY = "INTERCEPTOR_LOG_KEY";


        // 调用请求前
        @Override
        public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

            request.setAttribute(LOG_KEY, System.currentTimeMillis());
            return true;
        }

        // 调用完成后
        @Override
        public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {

            long start = (long) request.getAttribute(LOG_KEY);
            System.out.printf("Request[%s].cost= %d\n", ((HttpServletRequest) request).getRequestURL(), System.currentTimeMillis() - start);
        }
    }    
    ```

- `InterceptorConfig`

    ```java
    @Configuration
    public class InterceptorConfig implements WebMvcConfigurer {

        // 添加所有路径的日志拦截
        @Override
        public void addInterceptors(InterceptorRegistry registry) {
            registry.addInterceptor(new LogInterceptor()).addPathPatterns("/**");
        }
    }    
    ```




## ServletContext
### 启动
- 程序容器启动
    - Tomcat
    - Jetty

- 应用部署

- 应用加载
    - ServletContext 创建
    - 应用加载
        - `web.xml` 解析
        - `<servlet>`/ `<filter>`/ `<listener>` 实例化
            - 在服务器内存中进行
            - 实例化过滤器时，将由容器的一个新 `FilterConfig` 来调用应用的 `init()`

### 关闭
- 程序容器关闭
    - 应用卸载
        - `servlet|filter destroy()`
        - `ServletContext`/ `Servlet`/ `Filter`/ `Listener` 废弃

### 配置
#### load-on-startup
- 当 `<servlet><load-on-startup>` > 0，`Servlet.init()` 方法将于启动时使用一个新的 `ServletConfig` 来调用
    - 参数值代表序列值
        - 比如 1 表示第一个被实例化
        - 如若数值相同，则按 `web.xml` 出现的顺序进行加载
    - 而若 `load-on-startup` 未配置，则将于第一次 HTTP 请求命中时，调用 `init()` 方法


## HttpServlet(Request|Response)
- `Servlet` 容器监听于指定端口的 `HTTP` 请求命中时，调用
- 客户端发起 HTTP 请求
    - `Servlet` 容器创建 `HttpServletRequest` 和 `HttpServletResponse` 对象
    - 将对象传递给 `Filter` 链路
        - 过滤器未拦截，则通过调用 `chain.doFilter(request, response)` 继续执行
    - 最后传递给 `Servlet` 实例
        - 调用 Servlet.service() 方法
            - 通过 `request.getMethod()` 将请求分发给指定的 `doXxx()` 方法
                - 传递的内容包含 `HTTP.Request`/ `HTTP.Header`/ `HTTP.Body`
                - `HttpServletResponse` 可设置 `HTTP.Header`/ `HTTP.Body`
                - 请求完成后，`HttpServletRequest` 和 `HttpServletResponse` 对象均将被回收再利用
            - 如若特地方法不支持，则返回 `405 Method not allowed` 异常


## HttpSession
- 客户端首次访问应用
    - 通过 `request.getSession()` 创建获取 `HttpSession`
        - 生成长且唯一的 `Session.Id`，并存储于服务器内存中
    - 返回时将 `Session` 一并返回
        - 通过 `HTTP.Set-Cookie` 将 `session.id` 作为 `JSESSIONID` 返回
        - 服务端将读取 `cookie.JSESSIONID` 值并匹配内存中的 HttpSession

- 客户端再次请求
    - 只要 `cookie` 仍有效，就将其通过 `Http.Header.Cookie` 带回

- 服务器留存
    - `HttpSession` 将于未使用情况下， `web.xml <session-timeout>` 分钟后销毁
        - 默认的 `session.timeout` 为 30 分钟
    - 而在 `HttpSession` 过期后，客户端重新访问，将会生成以新的 `HttpSession`

- 客户端留存
    - 在浏览器实例运行其中，`session.cookie` 将始终保留


## 生命周期
### ServletContext
- 同容器一并销毁
- 于所有请求、会话中共享

### HttpSession
- `HttpSession` 于客户端交互期间存在
- 服务端的会话不存在过期
- 于同一会话的所有请求中共享

### HttpServlet(Request|Response)
- 于请求的发起时创建，于响应完成时销毁
- 不共享

### Servlet/ Filter/ Listener
- 同容器一并销毁
- 于所有的请求、会话中共享

### Attribute
- 于 `ServletContext`/ `HttpServletRequest`/ `HttpSession` 中创建的属性，于所属容器管理
    - 类似于对象管理框架中的对象，如 JSF、CDI、Spring 等

## 线程安全
- `Servlet` 和 `Filter` 于所有请求中共享
    - 通过多线程、不同进程在同一实例中进行支持
        - 实例的重新
    - 切匆将请求或会话域数据作为 Servlet 或过滤器的实例变量
        - 于所有请求、会话中共享，导致非线程安全


# 参考
- [How do servlets work?](https://stackoverflow.com/questions/3106452/how-do-servlets-work-instantiation-sessions-shared-variables-and-multithreadi/3106909#3106909)
- [Servlet 工作原理解析](https://www.ibm.com/developerworks/cn/java/j-lo-servlet/index.html)
- [Spring Boot Servlet](https://blog.csdn.net/catoop/article/details/50501686)
- [Java Servlet 技术简介](https://www.ibm.com/developerworks/cn/education/java/j-intserv/j-intserv.html)
- [Servlet 综述](https://blog.csdn.net/justloveyou_/article/details/60964714)
- [servlet的本质是什么，它是如何工作的](https://www.zhihu.com/question/21416727)
- [Java ---Filter过滤器](https://www.jianshu.com/p/85371385ae9e)
- [Spring Boot实战：拦截器与过滤器](https://www.cnblogs.com/paddix/p/8365558.html)
- [实战Spring Boot 2.0系列(五) - Listener, Servlet, Filter和Interceptor](https://juejin.im/post/5b2ddbcef265da59a76c92a4)
- [Java中的Filter 过滤器](https://tianweili.github.io/2015/01/26/Java%E4%B8%AD%E7%9A%84Filter-%E8%BF%87%E6%BB%A4%E5%99%A8/)
- [Java ---Filter过滤器](https://www.jianshu.com/p/85371385ae9e)
- [Java中的Listener 监听器](https://tianweili.github.io/2015/01/27/Java%E4%B8%AD%E7%9A%84Listener-%E7%9B%91%E5%90%AC%E5%99%A8/)
- [Spring Interceptor vs Filter 拦截器和过滤器区别 ](http://einverne.github.io/post/2017/08/spring-interceptor-vs-filter.html)
