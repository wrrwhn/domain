---
title: "Spring.Security"
date: "2018-06-04"
categories:
 - "整理"
tags:
 - "Spring"
 - "Security"
toc: true
---


# 前后端分离
## 思路
- 登录成功后，于 Session 添加相应属性值
- 接口请求时，判断过滤接口和 Session 属性值是否过期、有效，以判断是否拦截

## 选型
- Spring.Security
    - 展示型项目，简单区分前后端即可
- Vue+ Bootstrap

## 代码
### 引用
```
<parent>
    <version>1.3.8.RELEASE</version>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-core</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-test</artifactId>
        <version>4.0.4.RELEASE</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### 登录
- Controller
    ```
    @RequestMapping(value = "/login", method = RequestMethod.POST)
    public void login(@RequestBody UserVo userVo, HttpServletRequest request) {
        securityService.login(userVo.getName(), userVo.getPwd(), request);
    }
    ```

- Service   
    ``` 
    public void login(String username, String password, HttpServletRequest request) {

        User user = userService.findByName(username);
        if (null == user || StringUtils.isEmpty(user.getPwd()) || !passwordEncoder.matches(password, user.getPwd()))
            throw new IllegalStateException("用户名|密码 错误");

        logger.info("User[{}] login.success", username);

        HttpSession session = request.getSession();
        // TODO 优化为 JWT 等其它可验证的方式
        session.setAttribute(AuthConfig.authSessionKey, user.getId());
    }
    ```

### 鉴权
- Application
    ```
    @SpringBootApplication(exclude = {SecurityAutoConfiguration.class})
    @EnableTransactionManagement(proxyTargetClass = true)
    @EnableJpaRepositories(basePackages = "com.yao")
    @ComponentScan(basePackages = "com.yao")
    @EnableAsync
    public class Application {

        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @Bean
        public CommandLineRunner commandLineRunner(ApplicationContext ctx) {
            return args -> {
                System.out.println("Let's inspect the beans provided by Spring Boot:");
                String[] beanNames = ctx.getBeanDefinitionNames();
                Arrays.sort(beanNames);
                for (String beanName : beanNames) {
                    System.out.println(beanName);
                }
            };
        }
    }
    ```

- Config
    ```
    @Configuration
    public class WebAppConfig extends WebMvcConfigurerAdapter {

        @Autowired
        AuthenticationInterceptor authenticationInterceptor;

        @Override
        public void addInterceptors(InterceptorRegistry registry) {
            registry
                    // 添加拦截器
                    .addInterceptor(authenticationInterceptor)
                    .addPathPatterns("/**")
                    // 添加不需要校验的路径
                    .excludePathPatterns("/resource/**", "/order", "/user/**");
        }
    }
    ```

- Interceptor
    ```
    @Component
    public class AuthenticationInterceptor extends HandlerInterceptorAdapter {

        final Logger logger = LoggerFactory.getLogger(AuthenticationInterceptor.class);

        @Override
        public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

            HttpSession session = request.getSession();
            Long authUserId = (Long) session.getAttribute(AuthConfig.authSessionKey);

            // TODO 补充权限校验、用户 Session 校验等
            if (null != authUserId) {
                request.setAttribute(AuthConfig.authSessionKey, authUserId);
                return true;
            } else {
                logger.warn("UNAUTHORIZED session:{}", session.getId());
                response.sendError(HttpServletResponse.SC_UNAUTHORIZED, "UNAUTHORIZED");
                return false;
            }
        }
    }
    ```

# 其它方案
## Spring.Security
- `2.0.2.RELEASE`
    - 实践
        - 着重参考「Reference.Example」中的「Registration and Login Example with Spring Boot, Spring Security, Spring Data JPA, and HSQL」
    - 尝试未果
        - 权限配置未果
            - 使用 RESTFul 方式
            - 打算针对 `/{interface}` 放开，而限制 `/{interface}/{method}` 进行权限校验
            - 配置如下
                ```
                http.authorizeRequests()
                    .antMatchers("/resources/**", "/car", "/casus", "/msg", "/news", "/order", "/share", "/user", "/user/login").permitAll()
                    .anyRequest().authenticated()
                ```
            - 如若有知道配置哪边写错的，烦请留言告知

### Apache.Shiro
- 阶段功能已满足，未尝试

# Reference
## Office
- Spring.Security
    - [Spring Security Reference](https://docs.spring.io/spring-security/site/docs/5.0.3.RELEASE/reference/htmlsingle/)
    - [Java Configuration](https://docs.spring.io/spring-security/site/docs/current/reference/html/jc.html)
    - [spring-projects/spring-security](https://github.com/spring-projects/spring-security)
- Apache.Shiro
    - [Apache.Shiro](https://shiro.apache.org/)

## Example
- [Registration and Login Example with Spring Boot, Spring Security, Spring Data JPA, and HSQL](https://hellokoding.com/registration-and-login-example-with-spring-security-spring-boot-spring-data-jpa-hsql-jsp/)
- [前后端分离之Springboot后端](https://blog.csdn.net/jimo_lonely/article/details/78782262)
- [从 MVC 到前后端分离](http://www.importnew.com/21589.html)
- [Shiro安全框架入门篇](https://blog.csdn.net/u013142781/article/details/50629708)
- [跟我学 Shiro](http://wiki.jikexueyuan.com/project/shiro/)

## Other
- [Spring Security (三) 核心配置解读](https://cloud.tencent.com/developer/article/1034750)
- [关于Boot应用中集成Spring Security你必须了解的那些事](https://emacoo.cn/backend/spring-boot-security/)
- [JWT](http://domain.yqjdcyy.com/post/jwt/)