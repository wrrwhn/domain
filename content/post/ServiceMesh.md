+++
date = "2017-11-16T22:07:46+08:00"
title = "ServiceMesh"
draft = false
tags = ["阅读","Service Mesh"]
share = true
+++


# 参考
- [Java未来也许不再是电商的首选开发语言](http://newtech.club/2017/12/06/Java%E6%9C%AA%E6%9D%A5%E4%B9%9F%E8%AE%B8%E4%B8%8D%E5%86%8D%E6%98%AF%E7%94%B5%E5%95%86%E7%9A%84%E9%A6%96%E9%80%89%E5%BC%80%E5%8F%91%E8%AF%AD%E8%A8%80/)
- [从零开始k8s](https://www.kubernetes.org.cn/doc-11)

# 明细
## 首选语言
- 优点
	- JVM 运行时强大
	- 工具链成熟
	- Spring 庞大的生态
		- dubbo
		- Spring Cloud
- 缺点
	- 开发繁琐
	- 包体积大
	- 运行时开销大

## 框架
### Service Mesh
- 定义
	- 基础设施层，处理服务间通讯
	- 轻量网络代理，负责复杂服务拓扑的请求的`可靠传递`
	- `对应用程序透明`
	- 利于`多语言`应用共存
- 优点
	- `聚焦于业务逻辑`
	- 不再于开发层面关注负载均衡、路由、熔断、限流、服务注册
- 工具
	- Kubernetes 
