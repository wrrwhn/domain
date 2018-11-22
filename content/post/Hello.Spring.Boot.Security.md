---
title: "Hello.Spring.Boot.Security"
date: "2018-11-22"
categories:
 - "整理"
tags:
 - "Spring"
 - "Security"
toc: true
---

# 实现

## Authentication-Memory
### TODO
- 内置用户管理
- 分接口限制访问
- 限制角色访问
    - 非登录情况自动跳转至登录界面
- 指定密码编码器
    - 新版本中，不再默认以文本编码器作为默认编码器

### Maven
- `pom.xml`

    ```xml
		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-web</artifactId>
			<version>5.0.5.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-config</artifactId>
			<version>5.0.5.RELEASE</version>
		</dependency>
    ```


### Code
- `Application`

    ```java
    @SpringBootApplication
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }
    }
    ```
- `SecurityConfig`

    ```java
    @EnableWebSecurity
    public class SecurityConfig extends WebSecurityConfigurerAdapter {

        private static final String ROLE_USER = "USER";

        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http.authorizeRequests()
                    // 配置 /resource 开头的请求无需验证即可访问
                    .antMatchers("/resource/**").permitAll()
                    // 配置 /user 开头的请求均需验证才能访问
                    .antMatchers("/user/**").hasRole(ROLE_USER)
                    // 登录模式为跳转至内置的登录界面
                    .and().formLogin();
        }

        @Override
        protected void configure(AuthenticationManagerBuilder auth) throws Exception {
            // 内存模式存储验证信息
            auth.inMemoryAuthentication()
                    // 指定密码编码器，否则 v5.0 以上版本会提示 `There is no PasswordEncoder mapped for the id "null"`
                    .passwordEncoder(new BCryptPasswordEncoder())
                    // 配置内置用户 yao 以 USER 角色
                    .withUser(User.withUsername("yao").password(new BCryptPasswordEncoder().encode("lu")).roles(ROLE_USER));
        }
    }
    ```

### Invoke
- `/recourse`
    - 200
- `/user`
    - 跳转至内置登录界面 `/login`


## Authorization-Jdbc
### TODO
- 更新用户、角色信息来源为数据库
- 接口限定用户、权限访问
    - 角色配置时，例如为 `ADMIN`，但实际数据库配置中应加上 `ROLE_` 前缀
    - 默认表语句可参见 `org/springframework/security/core/userdetails/jdbc/users.ddl`
    - 若为自建表，需更新 `AuthenticationManagerBuilder.jdbcAuthentication()`
        - `usersByUsernameQuery`
        - `authoritiesByUsernameQuery`


### Maven
- `pom.xml`

    ```xml
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<!-- Security.* -->
		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-web</artifactId>
			<version>5.0.5.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-config</artifactId>
			<version>5.0.5.RELEASE</version>
		</dependency>

        <!-- H2.* -->
		<dependency>
			<groupId>com.h2database</groupId>
			<artifactId>h2</artifactId>
			<version>1.4.197</version>
		</dependency>

        <!-- JPA.* -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
    ```

### Properties
- `application.yml`

    ```yml
    spring:
        datasource:
            url: jdbc:h2:mem:h2test;DB_CLOSE_DELAY=-1
            platform: h2
            driver-class-name: org.h2.Driver
            username: sa
            password:
            schema: classpath:h2/schema.sql
            data: classpath:h2/data.sql
        h2:
            console:
            settings:
                web-allow-others: true
            path: /h2-console
            enabled: true
        jpa:
            database: h2
            database-platform: org.hibernate.dialect.H2Dialect
            show-sql: true
            # 无法正常请求数据时添加该行
            hibernate:
                ddl-auto: update    
    ```

- `h2/schema.sql`

    ```sql
    create table users(id bigint auto_increment, username varchar(255), password varchar(255), enabled boolean);
    create table authorities(id bigint auto_increment, username  varchar(255),authority varchar(255), UNIQUE(username,authority));
    ```

- `h2/data.sql`

    ```sql
    insert into users(username,password,enabled) values( 'admin', '$2a$10$BrpHlhYyofs0RSA72JXHxucWRSlHhqyPSdsfqqyY1DSUq5vEWDOH.', true);
    insert into users(username,password,enabled) values( 'user', '$2a$10$wBkWTgqlprm1xJCroQo80..KVo.X.gJEgifddua6BSopY7RHVcB0i', true);

    insert into authorities (username,authority) values('admin','ROLE_ADMIN');
    insert into authorities (username,authority) values('user','ROLE_USER');
    ```

### Code
- `SecurityConfig`

    ```java
    @EnableWebSecurity
    public class SecurityConfig extends WebSecurityConfigurerAdapter {

        private static final String ROLE_ADMIN = "ADMIN";
        private static final String ROLE_USER = "USER";

        @Autowired
        DataSource dataSource;

        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http.authorizeRequests()
                    // 设置权限为 USER，在内部会自动加上 ROLE_ 前缀，所以数据配置权限时应该为 ROLE_USER
                    .antMatchers("/user/role/user").hasAnyRole(ROLE_ADMIN, ROLE_USER)   // ADMIN 或 USER 均可
                    .antMatchers("/user/role/admin").hasAnyRole(ROLE_ADMIN)             // 仅 ADMIN 可访问
                    .antMatchers("/user/role/all").authenticated()                      // 登录用户均可访问
                    .anyRequest().permitAll()                                           // 所有用户均可
                    .and().formLogin();

            // H2 正常连接
            http.authorizeRequests().antMatchers("/h2-console/**").permitAll();
            http.csrf().ignoringAntMatchers("/h2-console/**");
            http.headers().frameOptions().sameOrigin();
        }

        @Override
        protected void configure(AuthenticationManagerBuilder auth) throws Exception {

            // auth.jdbc
            auth.jdbcAuthentication()
                    // 指定加密方式
                    .passwordEncoder(new BCryptPasswordEncoder())
                    .dataSource(dataSource)
                    .usersByUsernameQuery("select username,password,enabled from users where username= ?")
                    .authoritiesByUsernameQuery("select username,authority from authorities where username= ?")
            ;
        }
    }
    ```
- `H2DataSourceConfig`

    ```java
    @Configuration
    public class H2DataSourceConfig {

        @Value("${spring.datasource.url:}")
        private String sourceUrl;
        @Value("${spring.datasource.username:}")
        private String sourceUsername;
        @Value("${spring.datasource.password:}")
        private String sourcePassword;

        // 注册 H2 数据库连接，供配置验证时访问
        @Bean
        public DataSource initH2DataSource() {

            JdbcDataSource dataSource = new JdbcDataSource();
            dataSource.setURL(sourceUrl);
            dataSource.setUser(sourceUsername);
            dataSource.setPassword(sourcePassword);
            return dataSource;
        }
    }
    ```
### Invoke

| Interface          | Anonymous  | User       | Admin    |
|--------------------|------------|------------|----------|
| `/user/role/admin` | `HTTP.403` | `HTTP.403` | HTTP.200 |
| `/user/role/user`  | `HTTP.403` | HTTP.200   | HTTP.200 |
| `/user/role/all`   | HTTP.200   | HTTP.200   | HTTP.200 |



## Authorization-Redis

### TODO
- 配置 `Redis` 以缓存 `Spring Session` 信息
    - 解决服务重启后，已登录的服务端可正常再次访问的情况

### Maven
- `pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-core</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.session</groupId>
        <artifactId>spring-session</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.session</groupId>
        <artifactId>spring-session-data-redis</artifactId>
    </dependency>
    ```

### Properties
- `application.properties`

    ```properties
    spring.redis.host= 127.0.0.1
    ```

### Code

- `Application.java`

    ```java
    @SpringBootApplication
    // 配置 Session 的有效期
    @EnableRedisHttpSession(maxInactiveIntervalInSeconds = 8 * 3600)
    public class Application {

        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }
    }
    ```
- `ConfigInitializer.java`

    ```java
    @Configuration
    public class ConfigInitializer {

        // 指定密码加密方式
        @Bean
        public PasswordEncoder initPWDEncoder() {
            return new BCryptPasswordEncoder();
        }

        @Bean
        public CookieSerializer cookieSerializer() {
            DefaultCookieSerializer serializer = new DefaultCookieSerializer();
            serializer.setCookiePath("/");
            return serializer;
        }
    }
    ```
- `SecurityInterceptor.java`

    ```java
    @Configuration
    public class SecurityInterceptor implements HandlerInterceptor {

        // 接口调用前，判定是否已登录
        @Override
        public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

            HttpSession session = request.getSession();

            if (null != session.getAttribute(SecurityMvcConfig.SESSION_KEY)) {
                return true;
            }
            response.getWriter().write("Login first, please");
            return false;
        }
    }
    ```
- `SecurityMvcConfig.java`

    ```java
    @Configuration
    public class SecurityMvcConfig implements WebMvcConfigurer {

        public final static String SESSION_KEY = "USER_SESSION";

        @Override
        public void addInterceptors(InterceptorRegistry registry) {

            // 配置可直接访问的
            registry
                    .addInterceptor(new SecurityInterceptor())
                    .addPathPatterns("/**")
                    .excludePathPatterns(
                            "/user/login", "/user/logout",
                            "/redis/**"
                    );
        }
    }    
    ```
- `UserController.java`

    ```java
    @RestController
    @RequestMapping("/user")
    public class UserController {

        @RequestMapping(value = {"", "/"}, method = RequestMethod.GET)
        public List<String> list(HttpSession session) {
            return Stream.of(session.getAttribute(SecurityMvcConfig.SESSION_KEY).toString()).collect(Collectors.toList());
        }

        @RequestMapping(value = "/login", method = RequestMethod.POST)
        public void login(String username, String password, HttpSession session) {
            session.setAttribute(SecurityMvcConfig.SESSION_KEY, 1);
        }

        @RequestMapping(value = "/logout", method = RequestMethod.PUT)
        public void login(HttpSession session) {
            session.removeAttribute(SecurityMvcConfig.SESSION_KEY);
        }
    }
    ```



## Authorization-OAuth

### TODO
- 支持 OAuth 模式
    - 客户端模式
    - 密码模式

### Maven
- `pom.xml`

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.security.oauth</groupId>
        <artifactId>spring-security-oauth2</artifactId>
        <version>2.3.3.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis</artifactId>
    </dependency>
    ```

### Properties
- `application.properties`

    ```yml
    spring.redis.host= 127.0.0.1
    ```

### Code
- `ConfigInitializer.java`

    ```java
    @Configuration
    public class ConfigInitializer {

        @Bean
        public PasswordEncoder initPWDEncoder() {
            return new BCryptPasswordEncoder();
        }
    }
    ```
- `OAuth2Config.java`

    ```java
    @Configuration
    public class OAuth2Config {

        private static final String OAUTH_RESOURCE = "resource";
        private static final String AUTH_USER_CLIENT = "client";
        private static final String AUTH_USER_PWD = "password";

        @Configuration
        @EnableResourceServer
        protected static class ResourceServiceConfiguration extends ResourceServerConfigurerAdapter {
            @Override
            public void configure(ResourceServerSecurityConfigurer resources) throws Exception {
                resources.resourceId(OAUTH_RESOURCE).stateless(true);
            }

            // 仅配置 /user 下的接口，于鉴权情况下可供访问
            @Override
            public void configure(HttpSecurity http) throws Exception {
                http
                        .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.IF_REQUIRED)
                        .and().requestMatchers().anyRequest()
                        .and().anonymous()
                        .and().authorizeRequests().antMatchers("/user/**").authenticated();
            }
        }

        @Configuration
        @EnableAuthorizationServer
        protected static class AuthorizationServerConfiguration extends AuthorizationServerConfigurerAdapter {

            @Autowired
            AuthenticationManager authenticationManager;
            @Autowired
            RedisConnectionFactory redisConnectionFactory;
            @Autowired
            PasswordEncoder passwordEncoder;

            @Override
            public void configure(ClientDetailsServiceConfigurer clients) throws Exception {

                // 配置对外分配的应用（名称、授权方式、范围、密码）
                // 其中密码需使用密码编码器处理过
                clients.inMemory()
                        .withClient(AUTH_USER_CLIENT).resourceIds(OAUTH_RESOURCE).authorizedGrantTypes("client_credentials", "refresh_token").scopes("select").authorities("client").secret(passwordEncoder.encode(AUTH_USER_CLIENT))
                        .and().withClient(AUTH_USER_PWD).resourceIds(OAUTH_RESOURCE).authorizedGrantTypes("password", "refresh_token").scopes("select").authorities("client").secret(passwordEncoder.encode(AUTH_USER_PWD));
            }

            @Override
            public void configure(AuthorizationServerSecurityConfigurer security) throws Exception {
                // 允许客户端进行网页形式鉴权
                security.allowFormAuthenticationForClients();
            }

            @Override
            public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
                endpoints
                        .tokenStore(new RedisTokenStore(redisConnectionFactory))
                        .authenticationManager(authenticationManager);
            }
        }
    }
    ```
- `SecurityConfig.java`

    ```java
    @Configuration
    @EnableWebSecurity
    public class SecurityConfig extends WebSecurityConfigurerAdapter {

        @Autowired
        PasswordEncoder passwordEncoder;

        @Override
        protected void configure(AuthenticationManagerBuilder auth) throws Exception {

            // 配置内部用户账号
            auth.inMemoryAuthentication()
                    .passwordEncoder(passwordEncoder)
                    .withUser("admin").password(passwordEncoder.encode("admin")).roles("ADMIN")
                    .and().withUser("user").password(passwordEncoder.encode("user")).roles("USER");
        }

        @Override
        protected void configure(HttpSecurity http) throws Exception {

            // 开放鉴权相关接口
            http
                    .requestMatchers().anyRequest()
                    .and().authorizeRequests().antMatchers("/oauth/**").permitAll();
        }

        @Bean
        @Override
        public AuthenticationManager authenticationManagerBean() throws Exception {
            return super.authenticationManagerBean();
        }
    }
    ```
- `ResourceController.java`

    ```java
    @RestController
    @RequestMapping("/resource")
    public class ResourceController {

        @RequestMapping(value = "/{id}", method = RequestMethod.GET)
        public String get(@PathVariable @NotNull Long id) {

            Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
            return String.format("Resource[%s]: %s", id, authentication.getName());
        }

        @RequestMapping(value = "/{id}/detail", method = RequestMethod.GET)
        public String getDetail(@PathVariable @NotNull Long id) {

            Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
            return String.format("Resource[%s].detail: %s", id, authentication.getName());
        }
    }
    ```

- `UserController.java`

    ```java
    @RestController
    @RequestMapping("/user")
    public class UserController {

        @RequestMapping(value = "/{id}", method = RequestMethod.GET)
        public String get(@PathVariable @NotNull Long id) {

            Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
            return String.format("User[%s]: %s", id, authentication.getName());
        }
    }
    ```

### Invoke
- 认证

    | 认证模式 | 请求                                                                                                                               | 返回值                                                                                                                                                                                                |
    |--------|------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 客户端   | /oauth/token?<br>grant_type=client_credentials<br>scope=select<br>client_id=client<br>client_secret=client                          | {<br>"access_token": "93440a4e-3e9c-4a29-8e80-f03730d48fb1",<br>"token_type": "bearer",<br>"expires_in": 23224,<br>"scope": "select"<br>}                                                             |
    | 密码     | /oauth/token?<br>grant_type=password<br>scope=select<br>client_id=password<br>client_secret=password<br>username=user&password=user | {<br>"access_token": "f225c5f1-7be9-4835-8beb-1d929e6c6376",<br>"token_type": "bearer",<br>"refresh_token": "e0cc074f-8f14-4fd5-8ac3-c3442436bcef",<br>"expires_in": 23142,<br>"scope": "select"<br>} |

- 请求
    - 场景
        - 登录状态
            - `/user/1`
        - 请求时
            - `/user/1?access_token=0356302f-d4f1-4f1b-8e3a-c2a8270167a3`
    - 遍历

        | 接口                     | 未登录状态   | 登录状态                     | 请求时携带token       |
        |--------------------------|--------------|------------------------------|-----------------------|
        | `/user/{userId}`         | unauthorized | unauthorized                 | User[1]: `client`     |
        | `/resource/{resourceId}` | unauthorized | Resource[1]: `anonymousUser` | Resource[1]: `client` |




# 异常
## org.h2.jdbcx.JdbcDataSource 未找到

- 将 `scope` 移除，或手动更新为默认的 `compile`

    ```xml
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <version>1.4.197</version>
        <scope>runtime</scope>
    </dependency>
    ```

    


## No suitable driver found for

- 断点发现读取获取文件均为空
    - 将 `target` 目录删除，重新运行即可

## HasAnyRole 无法正常过滤，返回403

- 角色关联时，会自动添加 `ROLE_` 前缀，因为配置时需一致

    ```java
    http.authorizeRequests().antMatchers("/user/role/user").hasAnyRole(ROLE_ADMIN, ROLE_USER)
    insert into authorities (username,authority) values('admin','ROLE_ADMIN');

    ExpressionUrlAuthorizationConfigurer.java
        private static String hasAnyRole(String... authorities) {
            String anyAuthorities = StringUtils.arrayToDelimitedString(authorities, "','ROLE_");
            return "hasAnyRole('ROLE_" + anyAuthorities + "')";
        }
    ```


# 名词解析
- CSRF
    - Cross Site Request Forgery
    - 跨站请求伪造
        - 挟制用户在当前已登录的Web应用程序上执行非本意的操作的攻击方法
- CORS    
    - Cross-Origin Resource Sharing
    - 跨域资源共享
        - 允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制
- LDAP
    - Lightweight Directory Access Protocol
    - 轻量目录访问协议
        - 在公司计算机上登录一次，便可以自动在公司内部网上登录
        - 在多个服务中使用同一个密码

# 参考
## 官方
- [Spring Security Reference - 5.0.5](https://docs.spring.io/spring-security/site/docs/5.0.5.RELEASE/reference/htmlsingle/)
- [Spring Security](https://spring.io/projects/spring-security)
- [Spring Security Reference - 5.1.2](https://docs.spring.io/spring-security/site/docs/5.1.2.BUILD-SNAPSHOT/reference/htmlsingle/#community)
- [/spring-session/docs](https://docs.spring.io/spring-session/docs/)
- [H2.Features](http://www.h2database.com/html/features.html)
- [从零开始的Spring Security Oauth2（一）](http://blog.didispace.com/spring-security-oauth2-xjf-1/)

## 补充
- [理解OAuth 2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)
- [spring boot集成h2指南](https://segmentfault.com/a/1190000007002140)
- [Spring Data JPA(二)：SpringBoot集成H2](https://juejin.im/post/5ab4b339f265da238c3a9d0a)

## 异常
- [There is no PasswordEncoder mapped for the id "null"](https://blog.csdn.net/dream_an/article/details/79381459)
- [Why I can't get the org.h2.Driver? I use maven](https://stackoverflow.com/questions/32347944/why-i-cant-get-the-org-h2-driver-i-use-maven/34203570)
    - 将 H2.scope 由 test 更新为 runtime
- [springboot配置内存数据库H2](https://blog.csdn.net/zhanglf02/article/details/74502582)
    - 数据插入，但无法正常查询
- [H2 Database Console](https://springframework.guru/using-the-h2-database-console-in-spring-boot-with-spring-security/)
    - H2.Console 连接 403
- [H2 in-memory database. Table not found](https://stackoverflow.com/questions/5763747/h2-in-memory-database-table-not-found)
- [JdbcDaoImpl](https://docs.spring.io/spring-security/site/docs/4.2.5.RELEASE/apidocs/org/springframework/security/core/userdetails/jdbc/JdbcDaoImpl.html)
    - sql.init


## 代码
- [yqjdcyy/Hello_Spring_Boot](https://github.com/yqjdcyy/Hello_Spring_Boot)
- [4. Samples and Guides](https://docs.spring.io/spring-security/site/docs/5.0.5.RELEASE/reference/htmlsingle/#samples)
    - [Hello Spring Security](https://github.com/spring-projects/spring-security/tree/5.0.5.RELEASE/samples/javaconfig/helloworld)
    - [Hello Spring Security Boot](https://github.com/spring-projects/spring-security/tree/5.0.5.RELEASE/samples/boot/helloworld)
    - [Hello Spring Security XML](https://github.com/spring-projects/spring-security/tree/5.0.5.RELEASE/samples/xml/helloworld)
    - [Hello Spring MVC Security](https://github.com/spring-projects/spring-security/tree/5.0.5.RELEASE/samples/javaconfig/hellomvc)
    - [Custom Login Form](https://github.com/spring-projects/spring-security/tree/5.0.5.RELEASE/samples/javaconfig/form)
    - [OAuth 2.0 Login](https://github.com/spring-projects/spring-security/tree/5.0.5.RELEASE/samples/boot/oauth2login)