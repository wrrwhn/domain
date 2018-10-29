



# Introduction

providing both authentication and authorization to Java applications.

a comprehensive security solution for Java EE-based enterprise software applications

layers of security  
    each layer tries to be as secure as possible in its own right

authentication
    鉴定
    the process of establishing a principal is who they claim to be
authorization
    授权
    the process of deciding whether a principal is allowed to perform an action within your application

# FAQ

# Architecture

# Code

2.4 Getting Spring Security

2.4.3 Project Modules

5. Java Configuration

# OAuth2





# 实现

## Authentication-Memory
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

### Upgrade
- 验证方式自定义
- 验证持久化
- 用户数据源配置


## Authorization-Jdbc

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



## Authorization-Custom


## Authorization-Keep


# 异常


## org.h2.jdbcx.JdbcDataSource 未找到

    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <version>1.4.197</version>
        <scope>runtime</scope>
    </dependency>

    将 scope 移除，或手动更新为默认的 compile


## No suitable driver found for

断点发现读取获取文件均为空
将 target 删除，重新运行即可

## HasAnyRole 无法正常过滤，返回403

    http.authorizeRequests().antMatchers("/user/role/user").hasAnyRole(ROLE_ADMIN, ROLE_USER)
    
    insert into authorities (username,authority) values('admin','ROLE_ADMIN');

    ExpressionUrlAuthorizationConfigurer.java
        private static String hasAnyRole(String... authorities) {
            String anyAuthorities = StringUtils.arrayToDelimitedString(authorities, "','ROLE_");
            return "hasAnyRole('ROLE_" + anyAuthorities + "')";
        }



## OAuth2 实现
配置资源服务器
配置认证服务器
配置spring security

## OAuth2 模式
授权码模式
    - authorization code
简化模式
    - implicit
密码模式
    - resource owner password credentials
客户端模式
    - client credentials

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
- []()
- []()

## 补充
- [理解OAuth 2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)
- [spring boot集成h2指南](https://segmentfault.com/a/1190000007002140)
- [Spring Data JPA(二)：SpringBoot集成H2](https://juejin.im/post/5ab4b339f265da238c3a9d0a)
- []()
- []()

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
- []()
- []()




## 代码
- [4. Samples and Guides](https://docs.spring.io/spring-security/site/docs/5.0.5.RELEASE/reference/htmlsingle/#samples)
    - [Hello Spring Security](https://github.com/spring-projects/spring-security/tree/5.0.5.RELEASE/samples/javaconfig/helloworld)
    - [Hello Spring Security Boot](https://github.com/spring-projects/spring-security/tree/5.0.5.RELEASE/samples/boot/helloworld)
    - [Hello Spring Security XML](https://github.com/spring-projects/spring-security/tree/5.0.5.RELEASE/samples/xml/helloworld)
    - [Hello Spring MVC Security](https://github.com/spring-projects/spring-security/tree/5.0.5.RELEASE/samples/javaconfig/hellomvc)
    - [Custom Login Form](https://github.com/spring-projects/spring-security/tree/5.0.5.RELEASE/samples/javaconfig/form)
    - [OAuth 2.0 Login](https://github.com/spring-projects/spring-security/tree/5.0.5.RELEASE/samples/boot/oauth2login)
- []()
- []()
- []()
- []()