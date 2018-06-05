+++
date = "2018-06-05T15:00:00+08:00"
title = "Java.Filter-Lisenter-Inteceptor-AOP"
draft = false
tags = ["整理","Java"]
share = true
+++

[TOC]

# Compare
|             |   名称   |   机制   |                              适用范围                              |          生命周期         |           对象调用权限           |                            优点/场景                            |
|-------------|----------|----------|--------------------------------------------------------------------|---------------------------|----------------------------------|-----------------------------------------------------------------|
| Filter      | 过滤器   | 函数回调 | Servlet</br> 于 Servlet 前后作用                                   | 仅于 Servlet 初始化时调用 | Request/ Response                | 不依赖框架，便于移植</br> 字符编码设置、过滤敏感词、URL权限     |
| Listener    | 监听器   | 事件回调 | Servlet/ Application/ Swing</br>                                   | 初始化时调用              | 与事件相关                       | 不依赖框架，便于移植</br>统计在线人数、清理过期 Session         |
| Interceptor | 拦截器   | 反射机制 | Servlet</br> 于 Service.execute 前后                               | 多次调用                  | Request/ Response/ HandlerMethod | 不依赖框架，便于移植</br>效率检测、日志输出、安全检测、用户拦截 |
| AOP         | 面向切面 | 反射机制 | Servlet/ Application/ Swing</br>可深入至方法、异常抛出的前后，推荐 | 多次调用                  | Method.Name                      | 框架支持，灵活实现</br>                                         |

# Other
## Order
- Filter.before
- Interceptor.before
- AOP.before
- Request
	- Error
- AOP.after
- ControllerAdvice.handleError
- Interceptor.after
- Filter.after

# Example
## Filter
- init
```
public class TimeFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        long start = new Date().getTime();
        	chain.doFilter(request, response);
        System.out.println("time filter 耗时:"+ (new Date().getTime() - start));
    }

}
```

- config
```
@Configuration
public class WebConfig extends WebMvcConfigurerAdapter {
    
  	@Bean
    public FilterRegistrationBean timeFilter() {
        
        FilterRegistrationBean registrationBean = new FilterRegistrationBean();

        registrationBean.setFilter(new TimeFilter());
        List<String> urls = new ArrayList<>();
        urls.add("/*");
        registrationBean.setUrlPatterns(urls);

        
        return registrationBean;
    }
}
```

## Listener
- init
```
@WebListener
public class MyServetContextListener implements ServletContextListener{

    @Override
    public void contextInitialized(ServletContextEvent event) {
        ServletContext application = event.getServletContext();
        String userName = application.getInitParameter("userName");     
        System.out.println("启动web应用的用户名字为："+userName);
    }
}
```

## Inteceptor
- init
```
@Component
public class TimeInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {
        
        System.out.println(((HandlerMethod)handler).getBean().getClass().getName());
        request.setAttribute("startTime", new Date().getTime());

        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
            ModelAndView modelAndView) throws Exception {

        System.out.println("time interceptor 耗时:"+ (new Date().getTime() -  (Long) request.getAttribute("startTime")));
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

        System.out.println("time interceptor 耗时:"+ (new Date().getTime() - (Long) request.getAttribute("startTime")));
        System.out.println("ex is "+ex);
    }
}
```

## AOP
- init
```
@Aspect
@Component
public class TimeAspect {
    
    @Before()
    @Around("execution(* com.imooc.web.controller.UserController.*(..))")
    public Object handleControllerMethod(ProceedingJoinPoint pjp) throws Throwable {
        
        Object[] args = pjp.getArgs();
        for (Object arg : args) {
            System.out.println("arg is "+arg);
        }
        
        long start = new Date().getTime();
        Object object = pjp.proceed();
        System.out.println("time aspect 耗时:"+ (new Date().getTime() - start));
        
        return object;
    }
}
```

# Reference
- [filter(过滤器)与拦截器（AOP)区别](https://blog.csdn.net/heyeqingquan/article/details/71482169)
- [spring filter、interception、AOP之间对比](https://www.jianshu.com/p/cd9875bd7161)
- [從攔截過濾器到AOP](https://www.ithome.com.tw/node/84243)
- [Java三大器之拦截器(Interceptor)的实现原理及代码示例](https://blog.csdn.net/reggergdsg/article/details/52962774)
- [Java如何使用Listener](https://blog.csdn.net/zcl_love_wx/article/details/52072655)