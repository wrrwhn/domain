
## 构建 
### 代码
```
StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

    StreamExecutionEnvironment 
        // 程序执行上下文，提供并行度等配置，和运行/添加流的方法

    source.flatMap
        // 获取返回类型
        TypeInformation<R> outType = TypeExtractor.getFlatMapReturnTypes(...)
        transform(...)
            // 将操作节点添加至流转换列表中
            getExecutionEnvironment().addOperator(resultTransform)
            // 返回单输入流运算节点
            return new SingleOutputStreamOperator(environment, resultTransform)
```

### 串联方式
    `source-> transform*-> sink` 的 chain 式结构

## 执行
### 代码

// 查看运行拓扑图，数据如何流转，各算子并发度配置等
System.out.println("ExecutionPlan: " + env.getExecutionPlan());
env.execute();

### 通用

- StreamGraph
    // 根据代码生成数据流图，由StreamNode（数据节点，Source/Function/Sink）+ StreamEdge（传输策略，直连/Hash/轮询等）
    getStreamGraph()
        StreamGraphGenerator.generate(this, transformations)

    ```
    StreamNode[Source/1]->  StreamEdge[Rebalance]-> 
    StreamNode[FlatMap/2]-> StreamEdge[Hash]->  
    StreamNode[KeyedAggregation/2]->    StreamEdge[Forward]->    
    StreamNode[Sink/2]
    ```

- JobGraph
    // 优化流图为任务拓扑图，由 JobVertex（等价于StreamNode）+ IntermediateDataSet（节点计算结果）+ JobEdge（等价于StreamEdge）
    streamGraph.getJobGraph()
        StreamingJobGraphGenerator.createJobGraph(this)

    ```
    JobVertex[Source/1]->   IntermediateDataSet->   JobEdge[Rebalance]->   
    JobVertex[FlatMap/2]->   IntermediateDataSet->   JobEdge[Hash]->   
    JobVertex[KeyedAggregation->Sink/2]
    ```

- Configuration/MiniClusterConfiguration
    // 配置更新
    configuration.addAll(jobGraph.getJobConfiguration());
    configuration.addAll(...)       // 用户配置覆盖
    MiniClusterConfiguration.Builder()...

- start
    - abstract

### 本地场景

- LocalStreamEnvironment.execute()

    MiniCluster miniCluster = new MiniCluster(cfg);
    MiniCluster.start()
        commonRpcService= createRpcService(configuration, rpcTimeout, false, null)  // 通用 RPC 服务
        metricRegistry.startQueryService(commonRpcService.getActorSystem(), null);  // 初始化查询服务
        if (useSingleRpcService)...     // 根据 getRpcServiceSharing 配置来创建或共用同一 RPC 服务

### 远程场景


# 代码示例    

streaming.api.datastream
    DataStream
        StreamExecutionEnvironment environment
        StreamTransformation<T> transformation
        flatMap

    SingleOutputStreamOperator
        // 单输入流运算节点的接口

streaming.api.operators

streaming.api.graph
    StreamGraph
        Map<Integer, StreamNode> streamNodes
    StreamNode
        final int id
        Integer parallelism
        List<StreamEdge> inEdges
        List<StreamEdge> outEdges
        add[InEdge/OutEdge]
    StreamEdge
        StreamNode sourceVertex
        StreamNode targetVertex

flink.runtime.jobgraph
    JobVertex
        ArrayList<IntermediateDataSet> results
        createAndAddResultDataSet
    IntermediateDataSet
        JobVertex producer
        List<JobEdge> consumers
        addConsumer(JobEdge)
    JobEdge
        IntermediateDataSet source
        DistributionPattern distributionPattern
        JobVertex target

streaming.api.transformations
    StreamTransformation    // 代表一个流的操作，是 DataStream 的基础元件
        DataStream.map-> StreamTransformation 的树结构
        StreamGraphGenerator 将图转化为 StreamGraph

flink.runtime.minicluster

    MiniCluster
        // 本地执行 Flink 任务的类
        RpcService commonRpc/ jobManagerRpc/ taskManagerRpc/ resourceManagerRpc
        HeartbeatServices heartbeatServices
        volatile TaskExecutor[] taskManagers
        JobExecutionResult executeJobBlocking(JobGraph job)
        start()


flink.runtime.rpc
    RpcService
        <C extends RpcEndpoint & RpcGateway> RpcServer startServer(C rpcEndpoint)
        void/CompletableFuture execute(Runnable/Callable)
    RpcGateway
        getAddress()
        getHostname()
    RpcEndpoint abstract   
        // 
        final RpcServer rpcServer
        AtomicReference<Thread> currentMainThread



# 补充

## Akka
### 优势
- 异步/非阻塞/高性能的事件驱动编程模型
    - Actor 持有 Mailbox，消息同步投递至 Mailbox，Actor 异步消费 Mailbox 中的消息
    - 处理线程由所有 actor 共享，因此不推荐同步消息
- 轻量级事件处理
    - 1GB 可容纳百万级别个数的 Actor

### 类
- ActorSystem
    - 负责 Actor 的调度（分治）
    - 配置化启动
        - 使用 Typesafe 配置库
        - 日志级别/拦截器/邮箱
    - 日志存储
- Actor
    - 由 ActorSystem 创建，不允许直接新建
    - 路径定义
        - 树形结构进行组织

        | 类型 | 示例                                              | 解析                                                                                                                          |
        |----|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
        | 本地 | `akka://sys/user/helloActor`                      | sys: 创建 actor 的 ActorSystem 实现的名称<br>user: 由 actorOf 创建的均属于此类，系统创建的为 system<br>helloActor：自定义实现类 |
        | 远程 | `akka.tcp://sys@l27.0.0.1:2020/user/remoteActor ` | akka.tcp： 通信方式<br>`sys@l27.0.0.1:2020`： 远程服务的 ActorSystem 名称及其所在主机的 IP 和端口                               |

- ActorREf
    - Actor 的引用，实现与 Actor 通信的封装
        - 获取方式 
            - `ActorSystem.actorOf` 
            - `ActorContext.actorOf`


### 代码

```
ActorSystem system = ActorSystem.create("sys");
ActorRef helloActor = system.actorOf(Props.create(HelloActor.class), "helloActor");
// 异步调用，ask 为同步调用
helloActor.tell("hello helloActor", ActorRef.noSender());
system.terminate();
```

## Mesos 和 YARN 对比
### 对比
| 角度     | Mesos                                                                     | YARN                                                                                                      |
|--------|---------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| 资源调度 | framework 判断 mesos 提供的资源是否合适来确认是否执行<br>长期拒绝将被跳过 | Yarn 决定是否通过你的资源申请                                                                             |
| 框架角色 | 计算框架与 Mesos 一体<br>添加新计算框架，则需先在 Mesos 中部署             | 框架仅作为 client 的 Library 使用                                                                         |
| 隔离性   | 使用 Linux Container 对内存和 CPU 进行隔离                                | OS 进程级别隔离                                                                                           |
| 扩展性   | 50,000+节点                                                               | 6,000~100,000节点                                                                                         |
| 容错性   | ZK 对 master 容错<br> Slave 任务失败后转移<br>依赖各框架自身的容错机制    | ZK 对 ResourceManager(RM) 容错<br>ApplicationMaster(AM) 采集任务执行信息至磁盘<br>RM 监控 AM 进行重启恢复 |

### 架构图
- Mesos
    - ![mesos-architecture.jpg](http://doc.yqjdcyy.com/fff7dbf4-f326-4f47-82eb-ade76aafe661.jpg)
- Yarn
    - ![yarn-architecture.png](http://doc.yqjdcyy.com/fbf9b915-fc29-4fcc-bb08-f9c81dd6fe6a.png)


# 参考

## Flink
- []()
- []()
- []()


## Akka
- [Akka简介与Actor模型](http://www.godpan.me/2017/03/18/learning-akka-1.html)
- [Akka 中的 Actor 系统](https://scala.cool/2017/04/learning-akka-2/)
- []()

## Other
- [Yarn与Mesos的对比](https://www.jianshu.com/p/9c52edc12a9d)
- [Flink 底层RPC框架分析](https://www.cnblogs.com/leesf456/p/11120045.html)
- []()