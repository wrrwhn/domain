
# 作用
- 在高并发下，避免服务阻塞造成对整体服务稳定性的影响
- 通过服务降级、服务隔离来保障业务的顺利进行

# 选型
- 命令模式
    - ![611a83f9c0802df53c319a0198c6f5a5.png](http://incdn1.b0.upaiyun.com/2017/07/611a83f9c0802df53c319a0198c6f5a5.png)

- 线程|线程池
    - 客户端单独线程执行，避免阻塞影响调用线程的正常任务
    - 便于即时更新客户端库包
    - 避免调用端性能下降，反作用影响请求调用
    - 支持异步操作
        - 网络等操作大部分为同步执行

# 流程
## 流程图
- ![hystrix-command-flow-chart.png](http://otzm88f21.bkt.clouddn.com/48536c74-2638-42dd-9280-92269b033174.png)


## 节点
### *Command.init
- HystrixCommand
    - `HystrixCommand command = new HystrixCommand(arg1, arg2);`cls
- HystrixObservableCommand
    - `HystrixObservableCommand command = new HystrixObservableCommand(arg1, arg2);`

### *Command.execute
- HystrixCommand
    - `execute`
        - 同步请求
        - `K value = command.execute();`
    - `queue`
        - 异步请求
        - `Future<K> fValue = command.queue();`
- HystrixObservableCommand
    - `observe`
        - 订阅 `Observable`，返回源 `Observable` 的拷贝
        - `Observable<K> ohValue = command.observe(); `
    - `toObservable`
        - 订阅时，将执行 Hystrix 请求，并将返回值封装为 `Observable` 对象
        - `Observable<K> ocValue = command.toObservable();`

### Cache.Response
- 当允许缓存且当请求结果已缓存情况，直接返回 `Observable` 缓存结果

### Circuit.Open
- 检测融断状态
    - 已融断时，不再执行指令，并路由至 `Fallback`
    - 未融断则继续流转

### Thread Pool| Queue| Semaphore full
- 检测请求数是否已超出 Hystrix 限制
    - 已超出时，不再执行指令，并路由至 `Fallback`

### HystrixObservableCommand.construct() | HystrixCommand.run() 
- 返回结果
    - `HystrixCommand.run()`
        - 单一返回值和 `onCompleted ` 通知|异常
    - `HystrixObservableCommand.construct()`
        - 包含一个或多个返回值的`Observable`| `onError` 通知

- 执行超时情况下，将抛出 `TimeoutException` 异常，返回 `Fallback`
    - 若方法无法取消、中断，返回值亦将被废弃
- 注意
    - `Hystrix` 通过 `JVM` 抛出 `InterruptedException`
    - 但大多数 `HTTP` 调用库不支持对 `InterruptedException` 异常的处理
        - 因此需确保连接、读和写的的超时时长

### Circuit.Health calc
- Hystrix 将运行状态上报给融断器，以供进行统计决定融断器的状态更新

- 上报状态
    - 成功
    - 失败
    - 拒绝
    - 超时

- 融断状态
    - 闭合
        - 下一次健康
    - 打开
        - 短路
        - 后续请求无法调用请求，直到恢复期过去

### Fallback 

- 调用时机
    - 命令执行时抛出异常
    - 融断器短路
    - 命令线程池|队列|信号 超出阀值
    - 命令执行超时

- 方法内容
    - 通用返回值
    - 无网络请求、内存缓存、静态逻辑


### Response.success

- ![hystrix-return-flow-640.png](http://otzm88f21.bkt.clouddn.com/15179820-a8d3-4b25-a20c-79a33ef5016f.png)


# 细节
## 融断器

- 条件
    - 错误超过指定比例，默认为 `50%`
    - `10秒` 内超过 `20个` 请求异常

- 时机
    - 关闭-> 半开
        - 当请求量超出预定阀值
        - 当请求失败率直琿预定异常阀值比率
    - 半开
        - 短路状态
            - 所有请求将直接路由到 `fallback`
        - 经过指定休眠时间后，将放过下一请求
            - 请求失败
                - 半开-> 开启
            - 请求成功
                - 半开-> 关闭
    - 开启
        - 持续一个睡眠窗口的时间

- 结构
    - ![circuit-breaker-1280.png](http://otzm88f21.bkt.clouddn.com/755834b2-9adc-4ba6-a607-485d6270ff79.png)
    - 默认维护 10 个 `Bucket`，每秒一个 `Bucket` 以记录成功、失败、超时和拒绝的状态


## 隔离
- 线程隔离
    - 将执行依赖代码的线程，与请求线程（如 Tomcat、Jetty）分离，以自由控制离开的时间
    - 通过线程池大小控制并发，快速失败
    - 优点
        - 支持异步调用
        - 服务恢复时，仅需要清理线程池，便可恢复使用
    - 缺点
        - 增加 CPU、排队、调度和上下文切换
            - 实际使用中，开销足够小，不会造成重大成本和性能影响
- 信号隔离
    - 通过信号来限制并发访问
    - 使用请求线程执行依赖代码
    - 信息量大小可动态调整
        - 线程池大小不可动态调整
## 参数

# 应用

## Maven

    ```xml
    <!-- Hystrix.* -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-hystrix</artifactId>
        <version>1.4.5.RELEASE</version>
    </dependency>    
    ```

## Code

- `EurekaController`

    ```java
    // 服务请求相关配置
    @RequestMapping(value = "/hystrix/{method}", method = RequestMethod.GET)
    // 融断器配置
    @HystrixCommand(
            // 异常情况下的代理服务，**参数、返回值需一致**
            fallbackMethod = "fallback",
            commandProperties = {
                    // 设置超时判断时长
                    @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "1000")})
    public String hystrix(
            @RequestParam Boolean error,
            @RequestParam Integer duration) {

        Assert.isTrue(null != error && !error, "Hystrix.Error");
        if(null!= duration && duration> 0){
            Time.sleep(duration);
        }

        return feignService.config();
    }

    // 融断异常时的代理方法
    public String fallback(Boolean error, Integer duration) {
        return "Hystrix.Fallback";
    }
    ```

- `Application`

    ```java
    @EnableEurekaClient
    @SpringBootApplication
    @EnableFeignClients
    // 开启融断
    @EnableCircuitBreaker
    public class Application {

        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }
    }
    ```

## Request

- `/actuator/hystrix.stream`    
    - 

# 参考
## 原理
- [Home](https://github.com/Netflix/Hystrix/wiki)
- [How it Works](https://github.com/Netflix/Hystrix/wiki/How-it-Works)
- [Hystrix工作原理（官方文档翻译）](https://segmentfault.com/a/1190000012439580)

## 使用
- [Hystrix使用入门手册](https://www.jianshu.com/p/b9af028efebb)
- [使用 Hystrix 实现自动降级与依赖隔离](http://www.importnew.com/25704.html)
- []()
- []()
- []()

## 代码
- [spring cloud 学习(4) - hystrix 服务熔断处理](https://www.cnblogs.com/yjmyzz/p/spring-cloud-hystrix-tutorial.html)
- []()
- []()
- []()