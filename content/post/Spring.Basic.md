---
title: "Spring.Basic"
date: "2016-12-11"
categories:
 - "整理"
tags:
 - "Spring"
toc: true
---


## [AOP](http://my.oschina.net/huangyong/blog/161338)

### 介绍
- Aspect-Oriented Programming
- 面向切面编程，对着代码横着切入
- 对方法的增强为 Weaving （织入）
- 对类的增强为 Introduction （引入）

### 代理
#### 代码写死
- interface
``` java
public interface Greeting {  
 
    void sayHello(String name);  
} 
```
- implement
``` java
public class GreetingImpl implements Greeting {  
 
    @Override 
    public void sayHello(String name) {  
        before();  
        System.out.println("Hello! " + name);  
        after();  
    }  
 
    private void before() {  
        System.out.println("Before");  
    }  
 
    private void after() {  
        System.out.println("After");  
    }  
} 
```

#### 静态代理
- resolution
- 单独为 GeetingImpl 创建代理类
- proxy
``` java
public class GreetingProxy implements Greeting {  
 
    private GreetingImpl greetingImpl;  
 
    public GreetingProxy(GreetingImpl greetingImpl) {  
        this.greetingImpl = greetingImpl;  
    }  
 
    @Override 
    public void sayHello(String name) {  
        before();  
        greetingImpl.sayHello(name);  
        after();  
    }  
 
    private void before() {  
        System.out.println("Before");  
    }  
 
    private void after() {  
        System.out.println("After");  
    }  
} 
```
- call
``` java
Greeting greetingProxy = new GreetingProxy(new GreetingImpl());  
greetingProxy.sayHello("Jack");  
```

#### JDK 动态代理
- resolution
- 合并所有代理类功能至动态代理类中
- 只能代理接口
- proxy
``` java
public class JDKDynamicProxy implements InvocationHandler {  
 
    private Object target;  
 
    public JDKDynamicProxy(Object target) {  
        this.target = target;  
    }  
 
    @SuppressWarnings("unchecked")  
    public <T> T getProxy() {  
        return (T) Proxy.newProxyInstance(  
            target.getClass().getClassLoader(),   // 使用该实现类的接口实现
            target.getClass().getInterfaces(),   // 通用代理功能
            this  // 代理实现功能
        );  
    }  
 
    @Override 
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {  
        before();  
        Object result = method.invoke(target, args);  
        after();  
        return result;  
    }  
 
    private void before() {  
        System.out.println("Before");  
    }  
 
    private void after() {  
        System.out.println("After");  
    }  
} 
```
- call
``` java
Greeting greeting = new JDKDynamicProxy(new GreetingImpl()).getProxy();  
greeting.sayHello("Jack");  
```

#### CGLib 动态代理
- resolution
- 支持对任意类的代理
- proxy
``` java
public class CGLibDynamicProxy implements MethodInterceptor {  
 
    private static CGLibDynamicProxy instance = new CGLibDynamicProxy();  
 
    private CGLibDynamicProxy() {  
    }  
 
    public static CGLibDynamicProxy getInstance() {  
        return instance;  
    }  
 
    @SuppressWarnings("unchecked")  
    public <T> T getProxy(Class<T> cls) {  
        return (T) Enhancer.create(cls, this);  
    }  
 
    @Override 
    public Object intercept(Object target, Method method, Object[] args, MethodProxy proxy) throws Throwable {  
        before();  
        Object result = proxy.invokeSuper(target, args);  // 使用反射支持所有类
        after();  
        return result;  
    }  
 
    private void before() {  
        System.out.println("Before");  
    }  
 
    private void after() {  
        System.out.println("After");  
    }  
} 
```
- call
``` java
Greeting greeting = CGLibDynamicProxy.getInstance().getProxy(GreetingImpl.class);  
greeting.sayHello("Jack");  
```

### Spring AOP
#### 前置增强
- proxy
``` java
@Component
public class GreetingBeforeAdvice implements MethodBeforeAdvice {  

    @Override 
    public void before(Method method, Object[] args, Object target) throws Throwable {  
        System.out.println("Before");  
    }  
} 
```
- call
``` java
ProxyFactory proxyFactory = new ProxyFactory();     // 创建代理工厂  
proxyFactory.setTarget(new GreetingImpl());         // 射入目标类对象  
proxyFactory.addAdvice(new GreetingBeforeAdvice()); // 添加前置增强  

Greeting greeting = (Greeting) proxyFactory.getProxy(); // 从代理工厂中获取代理  
greeting.sayHello("Jack");      
```


#### 后置增强
- proxy
``` java
@Component
public class GreetingAfterAdvice implements AfterReturningAdvice {  
 
    @Override 
    public void afterReturning(Object result, Method method, Object[] args, Object target) throws Throwable {  
        System.out.println("After");  
    }  
} 
```
- call
``` java
ProxyFactory proxyFactory = new ProxyFactory();     // 创建代理工厂  
proxyFactory.setTarget(new GreetingImpl());         // 射入目标类对象  
proxyFactory.addAdvice(new GreetingAfterAdvice());  // 添加后置增强   

Greeting greeting = (Greeting) proxyFactory.getProxy(); // 从代理工厂中获取代理  
greeting.sayHello("Jack");      
```

#### 环绕增强
- resolution
- 前置增强+ 后置增强
- proxy
``` java
@Component
public class GreetingAroundAdvice implements MethodInterceptor {  
 
    @Override 
    public Object invoke(MethodInvocation invocation) throws Throwable {  
        before();  
        Object result = invocation.proceed();  
        after();  
        return result;  
    }  
 
    private void before() {  
        System.out.println("Before");  
    }  
 
    private void after() {  
        System.out.println("After");  
    }  
} 
```
- call
``` java
ApplicationContext context = new ClassPathXmlApplicationContext("aop/demo/spring.xml");  // 获取 Spring Context  
Greeting greeting = (Greeting) context.getBean("greetingProxy");  // 从 Context 中根据 id 获取 Bean 对象（其实就是一个代理）  
greeting.sayHello("Jack"); 
```

#### 抛出增强
- implements
``` java
@Component 
public class GreetingImpl implements Greeting {  
 
    @Override 
    public void sayHello(String name) {  
        System.out.println("Hello! " + name);  
 
        throw new RuntimeException("Error"); // 故意抛出一个异常，看看异常信息能否被拦截到  
    }  
}
```
- proxy
``` java
@Component 
public class GreetingThrowAdvice implements ThrowsAdvice {  
 
    public void afterThrowing(Method method, Object[] args, Object target, Exception e) {  
        System.out.println("---------- Throw Exception ----------");  
        System.out.println("Target Class: " + target.getClass().getName());  
        System.out.println("Method Name: " + method.getName());  
        System.out.println("Exception Message: " + e.getMessage());  
        System.out.println("-------------------------------------");  
    }  
} 
```

#### 引入增强
- interface
``` java
public interface Apology {  
 
    void saySorry(String name);  
} 
```
- proxy
``` java
@Component 
public class GreetingIntroAdvice extends DelegatingIntroductionInterceptor implements Apology {  
 
    @Override 
    public Object invoke(MethodInvocation invocation) throws Throwable {  
        return super.invoke(invocation);  
    }  
 
    @Override 
    public void saySorry(String name) {  
        System.out.println("Sorry! " + name);  
    }  
} 
```
- config
``` xml
<bean id="greetingProxy" class="org.springframework.aop.framework.ProxyFactoryBean"> 
    <property name="interfaces" value="aop.demo.Apology"/>          <!-- 需要动态实现的接口 --> 
    <property name="target" ref="greetingImpl"/>                    <!-- 目标类 --> 
    <property name="interceptorNames" value="greetingIntroAdvice"/> <!-- 引入增强 --> 
    <property name="proxyTargetClass" value="true"/>                <!-- 使用CGLib代理目标类（默认为 false，使用JDK动态代理接口） --> 
</bean> 
```
- call
``` java
ApplicationContext context = new ClassPathXmlApplicationContext("aop/demo/spring.xml");  
GreetingImpl greetingImpl = (GreetingImpl) context.getBean("greetingProxy"); // 注意：转型为目标类，而并非它的 Greeting 接口  
greetingImpl.sayHello("Jack");  

Apology apology = (Apology) greetingImpl; // 将目标类强制向上转型为 Apology 接口（这是引入增强给我们带来的特性，也就是“接口动态实现”功能）  
apology.saySorry("Jack");  
```
