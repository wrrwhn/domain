

# 概念
## RPC
### 定义
- **远程过程调用**
	- 「请求」「应答」服务器，部署在「请求」服务器上的应用，想要调用「应答」服务器上应用提供的函数方法
	- 由于不在一个内存空间，不能直接调用，需要通过网络来表达调用的语义和传达调用的数据

### 框架前缀
#### 要点
- 通讯
	- 主要是通过在客户端和服务器之间建立TCP连接，传输远程调用所有交换的数据
		- **按需连接**，调用结束后就断掉
		- **长连接**，多个远程过程调用共享同一个连接

- 寻址
	- 「请求」服务器上的应用告诉底层的RPC框架，连接到「应答」服务器的 IP、端口、方式和相关的方法
		- 基于`Web`服务协议栈的`RPC`，就要提供一个`endpoint URI`，或者是从`UDDI`服务上查找
		- `RMI`调用的话，还需要一个`RMI Registry`来注册服务的地址

- 序列化
	- 网络协议是**基于二进制**，内存中的参数的值要序列化成二进制的形式

- 反序列化
	- 对参数进行反序列化，恢复为内存中的表达方式，然后找到对应的方法（寻址的一部分）进行本地调用以得到返回值。

- 处理
	- 将反馈数据根据业务进行处理

#### 图解
- ![RPC流程.jpg](http://otzm88f21.bkt.clouddn.com/62787ef2-3fd7-47ea-a09b-7c874ed6a9bd.jpg)


### 协议
- CORBA
	- Common Object Request Broker Architecture
	- 通用对象请求代理架构标准
- Java RMI
	- Remote Method Invocation
	- 远程方法调用
- Web Service
	- **服务导向**架构
- Thrift
	- 接口描述语言和二进制通讯协议，用来定义和创建跨语言的服务
	- 远程过程调用框架
- Rest API
	- Representational State Transfer
	- 表现层状态转化
		- Representational
			- 表现层即资源
		- State Transfer
			- GET| POST| PUT| DELETE
	- **网站即软件**


## Netty
### 定义
- `Netty` 是提供一种事件驱动的，责任链式（也可以说是流水线）的网络协议实现方式

### 组成
- 网络协议
	- 传输层协议
	- 编码解码
	- 压缩解压
	- 身份认证
	- 加密解密
	- 请求的处理逻辑
- 责任链
	- Upstream
		- 从传输层，经过一系列步骤，如身份认证，解密，日志，流控，最后到达业务层
	- DownStream
		- 业务层返回后，又经过一系列步骤，如加密等，又回到传输层
### 图解
- ![Netty.Stream.jpg](http://otzm88f21.bkt.clouddn.com/790d3b4a-9024-4bb6-a675-ef6eb8b24130.jpg)



# 构架


|                  | Dubbo | Montan | rpcx | gRPC   | Thrift |
|:-----------------|:------|:-------|:-----|:-------|:-------|
| 开发语言         | Java  | Java   | Go   | 跨语言 | 跨语言 |
| 分布式(服务治理) | √     | √      | √    | ×      | ×      |
| 多序列化框架支持 | √     | √      | √    | ×      | ×      |
| 多种注册中心     | √     | √      | √    | ×      | ×      |
| 管理中心         | √     | √      | √    | ×      | ×      |
| 跨编程语言       | ×     | ×      | ×    | √      | √      |

>
为了跨语言，服务端可以用不同的语言实现，客户端也可以用不同的语言实现，不同的语言实现的客户端和服务器端可以互相调用。
基于同一个IDL，可以生成不同语言的代码，并且语言的支持也非常的多。
GRPC
支持服务治理，主要的精力放在服务发现、路由、容错处理等方面，主要围绕一个语言开发
DUBBO
框架特性、性能、成熟度、技术支持、社区活跃度





## GRPC
### 特点
- 主要面向移动应用开发并基于HTTP/2协议标准而设计，基于ProtoBuf(Protocol Buffers)序列化协议开发，且支持众多开发语言

### 性能
- 



## thirft
### 特点
- 跨语言的高性能的服务框架
广泛的应用

### 性能
- 


## dubbo
### 特点
- Java高性能优秀的服务框架
可以和 Spring框架无缝集成


### 性能
- 


## Motan
### 特点
- 新浪微博开源的一个Java 框架
起于2013年，2016年5月开源

### 性能
- 100,000,000,000+ 次/ 100+服务/ 天


## RPCX
### 特点
- Go语言生态圈的Dubbo
比Dubbo更轻量，实现了Dubbo的许多特性，借助于Go语言优秀的并发特性和简洁语法

### 性能
- 

## 
	protobuf
	

# 
## 场景
- 0ms
	- 简单业务，将请求值修改后返回
	- 测试框架流转中的数据传输、序列化和调度
- 10ms
	- 普通业务请求，如请求数据库等
	- 测试并发时框架的调度、处理能力
- 30ms
	- 复杂业务，如请求第三方服务
	- 测试并发时框架的调度、处理能力

## 工具
- Apache bench|

## 指标
- 吞吐率
- 并发连接数
- 延迟
- 延迟-P99

## 
99.9%的请求必须小于1ms，所有的平均时间必须小于1ms。两个条件的限制。

吞吐量的值必需有响应时间来卡
TP99小于100ms的时候，系统可以承载的最大并发数是1000qps。


# Reference
- RPC
	- [谁能用通俗的语言解释一下什么是 RPC 框架？](https://www.zhihu.com/question/25536695)
	- [远程过程调用协议](https://baike.baidu.com/item/%E8%BF%9C%E7%A8%8B%E8%BF%87%E7%A8%8B%E8%B0%83%E7%94%A8%E5%8D%8F%E8%AE%AE/6893245?fromtitle=RPC&fromid=609861)
	- [你应该知道的RPC原理](https://www.cnblogs.com/LBSer/p/4853234.html)
	- []()
	- []()
	- []()
	- []()

- GRPC
	- [官网](https://grpc.io/)
	- [API 文件就是你的伺服器，REST 的另一個選擇：gRPC](https://yami.io/grpc/)
	- []()
	- []()
	- []()
	- []()

- Netty
	- [The Netty Project API Reference (3.2.6.Final)](https://docs.jboss.org/netty/3.2/api/)
	- []()
	- []()
	- []()

- Apache.AB
	- 官方
		- [Apache AB](http://httpd.apache.org/)
		- [Apache HTTP 服务器 2.4 文档](http://httpd.apache.org/docs/2.4/)
		- []()
		- []()
		- []()
	- 其它
		- [超实用压力测试工具－ab工具](https://www.jianshu.com/p/43d04d8baaf7)
		- [开源性能测试工具 - Apache ab 介绍](http://www.cnblogs.com/jackei/archive/2006/07/18/454144.html)
		- []()
		- []()
		- []()

- Other
	- RPC 框架
		- [流行的rpc框架benchmark](http://colobu.com/2018/01/31/benchmark-2018-spring-of-popular-rpc-frameworks/)
		- [分布式RPC框架性能大比拼](http://colobu.com/2016/09/05/benchmarks-of-popular-rpc-frameworks/)
		- [JAVA中几种常用的RPC框架介绍](https://blog.csdn.net/zhaowen25/article/details/45443951)
		- []()
		- []()
	- 补充
		- [性能测试应该怎么做？](https://coolshell.cn/articles/17381.html)
		- [Why Averages Suck and Percentiles are Great](https://www.dynatrace.com/news/blog/why-averages-suck-and-percentiles-are-great/)
		- [Dubbo-性能测试报告 - 404](http://dubbo.io/User+Guide-zh.htm#UserGuide-zh-%E6%80%A7%E8%83%BD%E6%B5%8B%E8%AF%95%E6%8A%A5%E5%91%8A)
		- [dubbo-api性能测试报告](http://baozi.leanote.com/post/dubbo-api%E6%80%A7%E8%83%BD%E6%B5%8B%E8%AF%95%E6%8A%A5%E5%91%8A)
		- []()
		- []()
		- []()

