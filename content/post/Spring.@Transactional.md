---
title: "Spring.@Transactional"
date: "2018-05-22"
categories:
 - "整理"
tags:
 - "Spring"
toc: true
---


# 使用方式
- 将 `@Transactional` 注解添加至 **方法** 上
- 将 `@Transactional` 注解添加至 **类** 上
	- 该类中所有 `public` 类型方法均会被配置以相同的事务属性
	- 方法级别的事务属性 **覆盖** 类级别的事务属性

# 属性
## 属性列表

|     属性名      | 是否必输 |  默认值  |                                                 说明                                                |
|-----------------|----------|----------|-----------------------------------------------------------------------------------------------------|
| name            | Yes      |          | 关联指定名称的事务管理器</br> 可用通配符(*)来匹配多个事务管理器，如 `get*`, `on*Event`              |
| propagation     | No       | REQUIRED | 事务**传播**行为                                                                                    |
| isolation       | No       | DEFAULT  | 事务**隔离**等级                                                                                    |
| timeout         | No       | -1       | 事务**超时**时长，单位为`秒` </br> 如果超过该时长限制而事务未完成，则自动回滚                       |
| read-only       | No       | false    | 事务**是否只读**                                                                                    |
| rollback-for    | No       |          | 指定能**触发事务回滚**的异常类型</br>使用逗号(,)分隔，如 `com.foo.MyBusinessException,ServletException` |
| no-rollback-for | No       |          | 仅抛出指定的异常，而不回滚异常</br>写法同`rollback-for`                                             |


## 明细
### PROPAGATION
|        事务传播行为       |                                                                                                                            说明                                                                                                                           |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Propagation.REQUIRED      | 外部存在事务情况下，加入，否则自行创建</br>**默认**配置</br>虽然事务逻辑范围内，可独立决定回滚状态，但由于同属相同的物理事务，内容回滚标志会影响到外部事务的提交</br>当内部回滚，而外部不希望受影响，则外部事务需处理 `UnexpectedRollbackException ` 异常 |
| Propagation.NOT_SUPPORTED | 容器不为这个方法开启事务                                                                                                                                                                                                                                  |
| Propagation.REQUIRES_NEW  | 不管是否存在事务，都创建一个新的事务</br>内外事务**完全独立**，拥有独立的物理事务，允许独立地提交或回滚                                                                                                                                                   |
| Propagation.MANDATORY     | 必须在一个已有的事务中执行，否则抛出异常                                                                                                                                                                                                                  |
| Propagation.NEVER         | 必须在一个没有的事务中执行，否则抛出异常                                                                                                                                                                                                                  |
| Propagation.SUPPORTS      | 依赖于调用方的事务，有则加入，没有则不带事务                                                                                                                                                                                                              |
| Propagation.NESTED        | 允许使用单一物理事务，但包含有多个**保存点**以回滚</br> 仅适合于 **JDBC 3.0** 驱动程序，即 JDBC DataSourceTransactionManager                                                                                                                              |

### ROLLBACK-FOR
- 默认检测到抛出 RuntimeException 异常或 Error 时，回滚事务
- 指定异常子类，当检测到该类异常抛出时，事务进行回滚
	- 示例
		- `@Transactional(propagation= Propagation.REQUIRED,rollbackFor= MyException.class)`

### ISOLATION
|        事务隔离级别        |                         说明                        |
|----------------------------|-----------------------------------------------------|
| Isolation.READ_UNCOMMITTED | 读取未提交数据</br>基本**不可用**</br>**脏读**和 **不可重复读** |
| Isolation.READ_COMMITTED   | 读取已提交数据</br>**默认**配置</br>**不可重复读**和 **幻读**  |
| Isolation.REPEATABLE_READ  | 可重复读</br>**幻读**                                   |
| Isolation.SERIALIZABLE     | 串行化                                              |

- 补充
	- 脏读
		- 一个事务读取到**另一事务未提交**的更新数据
	- 不可重复读
		- 在同一事务中，多次读取同一数据返回的**结果有所不同**
			- 后续读取可以读到另一事务已提交的更新数据
	- 可重复读
		- 在同一事务中多次读取数据时，能够保证所读数据一样
		- 后续读取不能读到另一事务已提交的更新数据
	- 幻读
		- 一个事务读到**另一个事务已提交**的insert数据

# 原理
## 机制
- 生成
	- 使用 AOP 代码，在代码运行时生成代理对象
- 拦截
	- 代理对象根据 `@Transactional` 的属性来决定是否由 `TransactionInterceptor` 拦截
		- 创建事务
		- 执行目标方法逻辑
		- 判断执行过程是否出现异常
			- 提交事务
			- 回滚事务
- ![事务实现机制.jpg](http://otzm88f21.bkt.clouddn.com/f13bc7d3-ab03-4b28-94d4-238e5c2a7162.jpg)


## 代理类
- CglibAopProxy.intercept()
- JdkDynamicAopProxy.invoke()

## 事务管理框架
- PlatformTransactionManager
	- AbstractPlatformTransactionManager
		- DataSourceTransactionManager
- ![TransactionManager框架.jpg](http://otzm88f21.bkt.clouddn.com/87910f53-47c1-4bc1-bfac-09a739a7ef53.jpg)


# 注意事项
## @Transactional 注解仅过 **public** 方法有效
- `TransactionInterceptor`拦截前，代理类间接调用 AbstractFallbackTransactionAttributeSource.computeTransactionAttribute 校验
	```
	protected TransactionAttribute computeTransactionAttribute(Method method, Class<?> targetClass) {
			// Don't allow no-public methods as required.
			if (allowPublicMethodsOnly() && !Modifier.isPublic(method.getModifiers())) {
				return null;
			}
			...
	}
	```

## AOP 代理下，目标方法 **由外部调用**时才有效
- AOP代理下，目标方法由外部调用时，才能被 Spring 生成的代理对象列入管理
	- 同类无事务注解方法，调用有注解方法，后者事务被忽略
	- 示例
		```
		@Service
		public class OrderService {
		    private void insert() {
				insertOrder();
			}
			@Transactional
	    	public void insertOrder() {
	    		// logic.*
	       	}
		}
		```

## 可用 `AspectJ` 代替以解决以上两种不兼容



# 参考
- 官方
	- [Transaction Management](https://docs.spring.io/spring/docs/4.2.x/spring-framework-reference/html/transaction.html)
	- [16.5.7 Transaction propagation](https://docs.spring.io/spring/docs/4.2.x/spring-framework-reference/html/transaction.html#tx-propagation)
- 补充
	- [Spring @Transactional工作原理](http://www.importnew.com/12300.html)
	- [透彻的掌握 Spring 中@transactional 的使用](https://www.ibm.com/developerworks/cn/java/j-master-spring-transactional-use/index.html)
	- [Spring @Transactional原理及使用](http://tech.lede.com/2017/02/06/rd/server/SpringTransactional/)

