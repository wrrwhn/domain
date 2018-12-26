---
title: "Redis.持久化"
date: "2018-12-07"
categories:
 - "整理"
tags:
 - "Redis"
toc: true
---


# 介绍

- 类型安全
- 垃圾回收机制
- 包括 API、工具和广范使用的算法、机制和协议
- 密码学和公钥基础架构接口，安全应用开发的基础
- 认证和访问控制，防止资源不经验证地访问
- 通过标准接口接入`提供商`，便于应用简单获安全服务，而不需要了解其实现
	- 集中精力于集成安全到自己的应用，而无需实现复杂的安全机制


# Java 语言安全和字节码验证

## Java 语言
- 类型安全
- 易用
	- 内存的自动管理
	- 垃圾回收
	- 数组的范围检查


## 类型安全
- 针对类、方法、字段，定义以严格的访问限制
	
	| 访问等级    | 访问限制             |
	|-------------|-------------------|
	| private     | 仅当前类可用         |
	| `[package]` | 仅同个包下的类可访问 |
	| protected   | 同个包下，和其子类    |
	| public      | 无限制               |


## 字节码验证
- 编译器将 Java 代码编译为与机器无关的字节码形式
- `字节码校验器`用于确保 java 运行环境`只执行合法`的字节码数据
	- 符合 Java 语言规范
	- 不违背 Java 语言规则和命名空间限制
	- 检查内存管理违规操作、堆栈溢出和非法的数据类型转换

# 基础的安全体系架构

## 组成
- 密码学
- 公钥基础架构接口
- 验证
- 安全通信
- 访问控制

## 原则
- 实现独立
	- 应用可以不需要实现安全功能
	- 可以通过依赖多个独立的提供商以支持
		- 提供商实现了安全功能

- 实现互操作
	- 提供商可于应用互相操作
	- 提供商与应用不会互相绑定

- 算法可扩展
	- 支持依赖于未实现的新兴标准、专用服务
		- 支持自定义提供商

## 安全服务提供商
- `java.security.Provider` 指定了提供商的名称及其所实现的安全服务
- 按首选项设置被选择时的优先级
- 可指定服务提供商的名称

### 示例

- `MessageDigest.getInstance`

	```java
    Object[] objs = Security.getImpl(algorithm, "MessageDigest", (String)null);
		return GetInstance.getInstance(type, getSpiClass(type), algorithm).toArray();
	        return getInstance(Providers.getProviderList().getService(type, algorithm), type.spi);
				return new GetInstance.Instance(service.getProvider(), service.newInstance());
					static final Map<String,EngineDescription>knownEngines.get(type)
	                    Class<?> clazz = getImplClass();
	                    Constructor<?> cons = clazz.getConstructor(paramClass);
	                    return cons.newInstance(constructorParameter);
	```

- `DatatypeFactory.newInstance()`
	```java
    // CoreXMLDeserializers
    DatatypeFactory.newInstance()

    	// DatatypeFactory
    	return FactoryFinder.find(DatatypeFactory.class, DATATYPEFACTORY_IMPLEMENTATION_CLASS);

    		// FactoryFinder
    		// System.properties
			if(ss.getSystemProperty(factoryId) != null)
				return newInstance(DatatypeFactory.class, systemProp, null, true);

			// File.properties
			if(ss.doesFileExist($java.home/lib/jaxp.properties))
				cacheProps.load(ss.getFileInputStream($java.home/lib/jaxp.properties));
				if(cacheProps.getProperty(factoryId) != null)
					return newInstance(DatatypeFactory.class, factoryClassName, null, true);

			// Provider.exist
			if(findServiceProvider(DatatypeFactory.class) != null)
				return findServiceProvider(DatatypeFactory.class)

			// Provider.default
			return newInstance(DatatypeFactory.class, DATATYPEFACTORY_IMPLEMENTATION_CLASS, null, true);

				// Class
				type.cast(getProviderClass(DATATYPEFACTORY_IMPLEMENTATION_CLASS, cl, doFallback, useBSClsLoader);
	```

- SPI.custom

	- principles
		- 接口实现时，必须支持无参构造函数
		- `META-INF/services/` 目录下创建接口全路径名称文件
			- 内容为具体实现类的`全路径名`称
			- 文件编码限定为 `UTF-8`
		- 实现类为 jar 包形式，则需置于当前程序的 classpath 下
		- 使用 `ServiceLoader.load` 进行动态加载

	- code

		```java
		com.yao.pattern.SPI.service
			public interface SoundService {
				void introduce();
			}

			public class ServiceProvider {
				public static void main(String[] args) {

					ServiceLoader<SoundService> services = ServiceLoader.load(SoundService.class);
					Iterator<SoundService> it = services.iterator();
					while (null != it && it.hasNext()) {
						SoundService service = it.next();
						System.out.printf("\n%s: \n\t", service.getClass().getName());
						service.introduce();
					}
				}
			}

		com.yao.pattern.SPI.service.impl
			public class USSoundServiceImpl implements SoundService {
				@Override
				public void introduce() {
					System.out.println("Hello, Earth-Man");
				}
			}
			public class ZHSoundServiceImpl implements SoundService {
				@Override
				public void introduce() {
					System.out.println("你好，地球人！");
				}
			}

		resource/META-INF/services
			com.yao.pattern.SPI.service.SoundService
				# 中文版本
				com.yao.pattern.SPI.service.impl.ZHSoundServiceImpl
				# 英文版本
				com.yao.pattern.SPI.service.impl.USSoundServiceImpl
		```


# 密码学

## 支持类型

|                全称               | 简称 |                      描述                     |
|-----------------------------------|------|-----------------------------------------------|
| Message digest algorithms         |      | 信息摘要<br>输出长度固定<br>常见如 MD5/ SHA-1 |
| Digital signature algorithms      | DSA  | 数字签名                                      |
| Symmetric bulk encryption         |      | 对称块加密<br>常见如 DES                      |
| Symmetric stream encryption       |      | 对称流加密<br>常见如 RC4                      |
| Asymmetric encryption             |      | 非对称加密<br>常见如 RSA                      |
| Password-based encryption         | PBE  | 密码加密                                      |
| Elliptic Curve Cryptography       | ECC  | 椭圆曲线加密<br>常见如 ECDSA                  |
| Key agreement algorithms          |      | 键值协议算法                                  |
| Key generators                    |      | 键值生成器                                    |
| Message Authentication Codes      | MACs | 消息认证码                                    |
| (Pseudo-)random number generators |      | 伪随机数生成器                                |


## 用途
- 信息完整性校验
- 数字签名
- SSL 数字证书

## 图示
- ![RSA.png](http://doc.yqjdcyy.com/630f3ee3-2491-4571-98fb-fe07de4babd7.png)
- ![DSA.png](http://doc.yqjdcyy.com/024eaebd-87fe-4ac0-93c6-e0cd146e9308.png)

# 公钥基础设施

- 支持在公钥加密情况下，安全地进行信息的传递
- 支持身份（个人、组织等）与数字证书的绑定、验证
- 由密钥、证书、公钥加密及生成数字签名证书的认证机构组成

## 密钥和证书存储
- 支持密钥和证书的长期存储

	|              类              |                作用                |
	|------------------------------|------------------------------------|
	| java.security.KeyStore       | 密钥<br>受信任的证书               |
	| java.security.cert.CertStore | 无关的、非受信的证书<br>注销的证书 |

## PKI 工具

|                工具               |                  作用                  |
|-----------------------------------|----------------------------------------|
| keytool.exe<br>密钥和证书管理工具 |                                        |
|                                   | 生成密钥对                             |
|                                   | 生成证书<br>根据证书请求生成<br>自签名 |
|                                   | 导入<br>证书<br>证书回复               |
| jarsigner.exe                     |                                        |
|                                   | 为 jar 文件签名                        |
|                                   | 验证已签名的 jar 文件                  |


# 身份验证

- 可通过插件式登录模块进行用户的身份验证
	- 调用 LoginContext 反向引用配置，以生成具体的 LoginModule.spi 实现类来执行验证
- 没有注册为 Security Provider，而是由各自的配置所管理

	|       实现类        |                适用场景               |
	|---------------------|---------------------------------------|
	| Krb5LoginModule     | 基于 Kerberos 协议                    |
	| JndiLoginModule     | 基于 LDAP/ NIS 数据的用户名、密码验证 |
	| KeyStoreLoginModule | 可登录至任意类型密钥存储              |



# 安全通信

- 数据于网络传输过程中，可能被非预期的接收者访问
	- 保证私人信息的安全性
		- 非验证方无法读取
		- 传输过程中数据未被修改

## SSL/TLS

- 功能
	- 数据加密
	- 信息校验
	- 服务端身份验证
	- 客户端身份验证
		- 可选

- 工具类
	- SSLSocket
		- 于 Socket 之上封装 SSL/TLS 协议
	- SSLEngine
		- 可用于生产和消费 SSL/TLS 数据包
	- KeyManager 
		- 用于管理用于验证的密钥
	- TrustManager 
		- 通过所管理的密钥中的证书，来判断是否可以信息

- 协议
	- SSLv3
	- TLSv1
	- TLSv1.1
	- TLSv1.2

## SASL
	
### 介绍	
- Simple Authentication and Security Layer
- 简单认证和安全层
- 使用账号和密码进行用户身份认证的机制，但为保障安全，对密码进行指定编码的加密

### 功能
- 认证
- 数据加密

### 工具类
- Sasl
	- 创建客户端和服务端对象

### 机制

|   端   |    机制    |             补充             |
|--------|------------|------------------------------|
| 客户端 |            |                              |
|        | CRAM-MD5   |                              |
|        | DIGEST-MD5 | 共享密钥，加密密码后传递比较 |
|        | EXTERNAL   |                              |
|        | GSSAPI     |                              |
|        | NTLM       |                              |
|        | PLAIN      | 密码明文                     |
| 服务端 |            |                              |
|        | CRAM-MD5   |                              |
|        | DIGEST-MD5 |                              |
|        | GSSAPI     |                              |
|        | NTLM       |                              |


## GSS-API
### 介绍
- Generic Security Service Application Programming Interface
- 通用安全服务应用程序接口
- 基于 Kerberos v5 机制
- 通过 `org.ietf.jgss.GSSContext` 对象来建立和维护建立安全上下文所需的共享信息

### 工具类
- kerberos
	- KerberosPrincipal
	- KerberosTicket
	- KerberosKey


# 访问控制

## 介绍
- 用于保护敏感资源（本地文件），或敏感的应用程序代码
- 由 SecurityManager 判断是否有权访问
	- 须安装至运行时，以激活访问控制检测
		- 其中 Applet 和 Java Web Start 程序均自动
		- 本地应用程序默认不运行
		- 但可程序中调用 setSecurityManager 方法，或于命令行中补充 -Djava.security.manager

## 权限
	
- 加载后的关联信息
	- 代码的加载来源
	- 代码的签名者
	- 默认的代码权限
		- 可与源主机的网络连接
			- 针对下载来的代码
		- 当前及其子目录的访问

- 加载与用户验证是分离的
	- 但可通过 Subject.doAs 动态关联用户与被执行的代码

## 策略
- 于类加载时，由类加载器授予
- 运行时，有且仅有一个 Policy 对象被加载
- 默认策略
	- 读取本地的 ASCII/UTF-8 文件读取安全配置
	- 可通过 Policy Tool 生成


# 补充
- CA
	- Certificate Authority
	- 数字证书认证机构

- PKI
	- Publish Key Infrastructure
	- 公钥基础设施

- CRLs
	- Certificate Revocation Lists
	- 证书注销列表

- LDAP
	- Lighweight Directory Access Protocol
	- 轻量目录访问协议

- NIS
	- Network Infomation Service
	- 网络数据服务协议

- SSL
	- Secure Sockets Layer
	- 安全套接层

- TLS
	- Transport Layer Security
	- 传输层安全


# 参考
## 文档
- [Java™ Security Overview](https://docs.oracle.com/javase/8/docs/technotes/guides/security/overview/jsoverview.html)
- [Java Security 总纲](https://joshuasabrina.iteye.com/blog/1798245)

## SPI
- [Service Provider 机制](https://zhuanlan.zhihu.com/p/20295483)
- [Java SPI](https://blog.csdn.net/top_code/article/details/51934459)
- [Java Cryptography Architecture Oracle Providers Documentation for JDK 8](https://docs.oracle.com/javase/8/docs/technotes/guides/security/SunProviders.html#SunPKCS11Provider)

## 密码学
- [数字签名算法介绍和区别](https://zhuanlan.zhihu.com/p/33195438)
- [数字签名是什么](http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html)
- [HTTPS证书生成原理和部署细节](https://www.barretlee.com/blog/2015/10/05/how-to-build-a-https-server/)

## 安全通信
- [SASL讲解，以及在Spark中的应用](https://blog.csdn.net/qq_21383435/article/details/80056186)

## 安全访问
- [Default Policy Implementation and Policy File Syntax](https://docs.oracle.com/javase/7/docs/technotes/guides/security/PolicyFiles.html)
- [Permissions and Security Policy](https://docs.oracle.com/javase/7/docs/technotes/guides/security/spec/security-spec.doc3.html)
- [Java安全之认证与授权](https://blog.csdn.net/xtayfjpk/article/details/45872161)