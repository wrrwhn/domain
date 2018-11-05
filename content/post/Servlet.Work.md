

# 整体

- Tomcat 容器模型
    - ![tomcat-container.jpg](http://doc.yqjdcyy.com/e6aa9c86-f4bf-4f50-9040-67a3bedff6b7.jpg)


# 

Tomcat.start()
    Tomcat.getServer()
        Tomcat.server = new StandardServer()
            StandardServer.globalNamingResources= new NamingResourcesImpl();
            if(isUseNaming())
                StandardServer.namingContextListener = new NamingContextListener();
    Tomcat.getConnecotr()
    Tomcat.server.start()


# 细节
## Servlet
### 作用

### 



## Filter

## Listener


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

### Servlet/ Servlet/ Servlet
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
- []()
- []()
- []()
- []()

