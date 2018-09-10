---
title: "Hello.Spring 30%"
date: "2017-08-09"
categories:
 - "整理"
tags:
 - "框架"
 - "Spring"
toc: true
---

# 组成


# 原理
## AOP
### 描述
- Aspect Orient Programming
- 面向方面（切面）编程，用于处理系统中分布中各模块的横切关注点，如事务、日志、缓存、对象池

### 类型
- 静态
    - 使用 `AOP` 框架提供的命令进行编译，从而在编译阶段生成 `AOP` 代理类
    - 编译时增强
    - AspectJ
- 动态
    - 运行时借助于 `JDK` 动态代理、`CGLIB`等在内存临时生成动态代理类
        - 通过注解以定义 方面（Aspect）、切入点（Pointcut）和增强处理（Advice）
    - 运行时增强
    - Spring AOP
        - JDK 动态代理
            - 要求被代理类必须有一个实现接口
            - 通过 `Proxy.newProxyInstance(ClassLoader, Class<?>[], InvocationHandler h)` 实现
        - CGLIB 动态代理
            - 底层通过 `asm` 来生成
            - 生成类效率较低（但可通过将生成类缓存以提升效率），执行时效率较高
            - 通过继承方式动态代理，若类被标记为 `final` 则无法使用
            - 具体通过 `implements MethodInterceptor` 和 `Enhancer` 生成

### 实现
- AspectJ

    ```java
    // 1.基类
    public class Hello {
        public static void main(String[] args) {
            new Hello().say();
        }
        public void say() {
            System.out.println("Hello");
        }
    }

    // 2.切面设置类
    public aspect HelloAspect {
        void around():call(void Hello.say()){
            System.out.println("Hello.Aspect start");
            proceed();
            System.out.println("Hello.Aspect end");
        }
    }

    // 3. Intellij 为当前项目设置 Aspecj 处理
        // 3.1 到 AspectJ 官网下载 Jar 包
        // 3.2 参照 《Creating a Library for aspectjrt.jar》将 =aspectjrt.jar 作为 library 添加至当前项目

    // 4.直接运行 Hello 项目即可
        // Hello.Aspect start
        // Hello
        // Hello.Aspect end
    ```

- Spring.AOP

    - Commons

        ```java
        // * 必须指定作用于方法，于运行时生效
        @Target(ElementType.METHOD)
        @Retention(RetentionPolicy.RUNTIME)
        public @interface Timer {
        }

        @Aspect
        @Component
        public class Advice {

            // 指定横切点，此处为对指定注解生效
            @Pointcut("@annotation(com.yao.aop.spring.Timer)")
            public void pointcut() {
            }

            // 对指定横切点增强
            @Before("pointcut()")
            public void before() {
                System.out.println("\tAdvice.before");
            }
        }
        ```

    - JDK

        ```java
        public interface Person {
            String say(String name);
        }

        // JDK 接口对象创建时，必须创建实现类以供 jdk 调用生成
        @Component
        public class Chinese implements Person {

            @Timer
            @Override
            public String say(String name) {

                name = "中国人讲：" + name;
                System.out.println(name);
                return name;
            }
        }
        ```

    - CGlib

        ```java
        // 只需注意非 final 类型即可
        @Component
        public class American {

            @Timer
            public String say(String name) {

                name = "American.say: " + name;
                System.out.println(name);
                return name;
            }

            public void eat(String food) {
                System.out.println("I'm eating " + food);
            }
        }        
        ```

    - Call

        ```java

        @SpringBootApplication
        @ComponentScan
        // 切面注解自动代理
        @EnableAspectJAutoProxy
        @RestController
        public class AOPApplication {

            @Autowired
            private Person chinese;
            @RequestMapping("/aop/spring/jdk")
            public void jdk() {
                chinese.say("Hi");
                System.out.println(chinese.getClass());
            }

            @Autowired
            private American american;
            @RequestMapping("/aop/spring/cglib")
            public void cglib() {
                american.say("Hi");
                american.eat("potato");
                System.out.println(american.getClass());
            }

            public static void main(String[] args) {
                SpringApplication.run(AOPApplication.class, args);
            }
        }
        ```        

## 详解
### Spring.AOP
#### 关键词
- Aspect
    - 横切关注点的抽象
- JoinPoint
    - 拦截点
        - 方法
        - 字段
        - 构造函数
    - Spring 仅支持方法拦截
- PointCut
    - 对拦截点的定义
- Advice
    - 通知、增强
    - 类型
        - `Before`
        - `After`
        - `After-Returning`
        - `After-Throwing`
        - `Around`
- Target
    - 代理的目标对象
- Weave
    - 织入
    - 将切面应用到目标对象，并代理对象创建的过程
- Introduction
    - 运行期，动态地为类添加字段、方法

#### 注解
##### PointCut
- 用法
    - `@Pointcut("within(@org.springframework.stereotype.Repository *)")`
    - `@Around("repositoryClassMethods()")`
    - xml
        
        ```xml
        <aop:config>
            <aop:pointcut id="anyDaoMethod" expression="@target(org.springframework.stereotype.Repository)"/>
        </aop:config>
        ```
- 标示符
    - `execution`
        - 描述
            - 通用匹配
        - 格式
            - `@Pointcut("execution(<作用域> <返回值> <路径>.<类>.<方法>(<参数>))")`
        - 参数
            - 作用域
                - public
                - private
                - protected
            - 返回值
                - Java 对象
            - 路径
                - 包路径
            - 类
                - 类名
            - 方法
                - 方法名
            - 参数
                - Java 对象
        - 示例
            - `@Pointcut("execution(* org.baeldung.dao.FooDao.*(..))")`
                - 针对 FooDao 的任意返回值、任意参数的任意方法生效
    - `within`
        - 描述
            - 限制于指定匹配类型
        - 格式
            - `@Pointcut("within(<路径>.<类>)")`
        - 示例
            - `@Pointcut("within(org.baeldung..*)")`
                - 匹配 org.baeldung 子目录下的任意类型
    - `this|target`
        - 描述
            - 匹配指定类型的子类实现
            - this 创建以 CGLIB 为基础的代理实现
                - 不要求一定要实现接口
            - target 创建以 JDK 为基础的代理实现
                - 要求一定要实现接口
        - 格式
            - `@Pointcut("this|target(<路径>.<类>)")`
        - 示例
            - `public class FooDao implements BarDao {}`
                - `@Pointcut("target(org.baeldung.dao.BarDao)")`
            - `public class FooDao {}`
                - `@Pointcut("this(org.baeldung.dao.FooDao)")`
    - `args`
        - 描述
            - 用于匹配指定的方法参数
        - 格式
            - `@Pointcut("execution(* *..<方法>(<参数>))")`
        - 示例
            - `@Pointcut("execution(* *..find*(Long))")`
                - 匹配任意以 find 开头，且仅有一个 Long 型参数的方法
            - `@Pointcut("execution(* *..find*(Long,..))")`
                - 匹配任意以 find 开头，且首个参数为 Long 型的方法
    - `@target`
        - 描述
            - 指定包含指定注解的类
        - 格式
            - `@Pointcut("@target(<注解>)")`
        - 示例
            - `@Pointcut("@target(org.springframework.stereotype.Repository)")`
                - 匹配添加有 Repository 注解的类

    - `@args`
        - 描述
            - 匹配指定注解的对象作用入参类型的方法
        - 格式
            - `@Pointcut("@args(<注解>)")`
        - 示例

            ```java
            @PrintArgs(name = "input")
            public class Input{}
            @Before("@args(input)")
            ```

    - `@within`
        - 描述
            - 指定注解的方法于本类或子类中调用时匹配调用
        - 格式
            - `@Pointcut("@within(<注解>)")`
        - 示例
            - `@Pointcut("@within(org.springframework.stereotype.Repository)")`

    - `@annotation`
        - 描述
            - 匹配拥有指定注解的方法
        - 格式
            - `@Pointcut("@annotation(<注解>)")`
        - 示例
            - `@Pointcut("@annotation(org.baeldung.aop.annotations.Loggable)")`
- 匹配
    - `*`
    - `..`
        - 作为参数时，代表任意参数形式
        - 作用路径时，代表子目录
    - `+`
        - 指定类型的子类型
    - 通过 `&&` `||` `!` 以组合指示符

        ```java
        @Pointcut("@target(org.springframework.stereotype.Repository)")
        public void repositoryMethods() {}

        @Pointcut("execution(* *..create*(Long,..))")
        public void firstLongParamMethods() {}

        @Pointcut("repositoryMethods() && firstLongParamMethods()")
        public void entityCreationMethods() {}
        ```






# 参考

## Spring
- [Spring 框架简介](https://www.ibm.com/developerworks/cn/java/wa-spring1/index.html)
- []()
- []()

## AOP
- [Spring AOP的实现原理](http://www.importnew.com/24305.html)
- [Spring AOP 实现原理与 CGLIB 应用](https://www.ibm.com/developerworks/cn/java/j-lo-springaopcglib/index.html)
- [AspectJ 官网](http://www.eclipse.org/aspectj)
- [IntelliJ Idea 下配置 AspectJ，及简单 demo](http://baimoz.me/1057/)
- [Creating a Library for aspectjrt.jar](https://www.jetbrains.com/help/idea/creating-a-library-for-aspectjrt-jar.html)
- [Implementing a Custom Spring AOP Annotation](https://www.baeldung.com/spring-aop-annotation)
- [Introduction to Pointcut Expressions in Spring](https://www.baeldung.com/spring-aop-pointcut-tutorial)
- [Spring Aop（三）——Pointcut表达式介绍](https://blog.csdn.net/elim168/article/details/78150438)
    - 针对 PointCut 的中文详解，包含示例
- [Spring AOP + AspectJ annotation examp`le](https://www.mkyong.com/spring3/spring-aop-aspectj-annotation-example/)`
- [AOP的底层实现-CGLIB动态代理和JDK动态代理](https://blog.csdn.net/dreamrealised/article/details/12885739)

## 
- []()

## 整理
- []()