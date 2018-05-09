+++
date = "2018-04-26T00:30:00+08:00"
title = "Java.Keyword"
draft = false
tags = ["整理","Java"]
share = true
+++

[TOC]

# List
## Access
- private
- protected
- public

## class, method, variable
- abstract
- class
- extends
- implements
- interface
- native
	- The native keyword may be applied to a method to indicate that the method is implemented in a language other then Java.
	- 用于说明该方法为原生函数，由 C|C++ 实现并编译成 DLL 提供给 Java 进行调用
		- 统一由**操作系统**实现
- new
- static
- strictfp
	- strict float point | 精确浮点
	- 确保数值计算均严格遵守 **FP-strict** 限制，并符合 **IEEE-754** 规范
	- 保障不会因为不同的硬件平台导致计算结果不一致
	- 可声明于类、接口或方法上
- synchronized
	- The synchronized keyword may be applied to a method or statement block and provides protection for critical sections that should only be executed by one thread at a time.
	- 作用于一个**方法**或**代码块**上，以保证同一时刻最多只有**一个线程执行**该代码
		- 若作用于静态方法，则整个类都会被锁定
- transient
	- The transient keyword may be applied to member variables of a class to indicate that the member variable should not be serialized when the containing class instance is serialized.
	- 实现 Serilizable 接口的对象中，有 transient 修饰的字段，则在自动序列化过程中略过
	- 实现 Externalizable 接口，序列化操作均于 writeExternal 手动序列，则与 transient 修饰无关
- volatile
	- The volatile keyword may be used to indicate a member variable that may be modified asynchronously by more than one thread.
 	- volatile is intended to guarantee that **all threads see the same value of the specified variable**
 	- 使用条件
 		- 对变量的写操作**不依赖于当前值**
 		- 该变量不包含在具有**其它变量**的公式中
## flow
- break
- switch
- case
- continue
- default
	- switch 的默认处理环节
	- 默认方法，用于添加新的功能至现有库的接口中，以兼容历史版本
		- **java 1.8 新增**
		- 如 `list.forEach`
- do
- else
- for
- if
- instanceof
	- The instanceof keyword is used to **determine the class** of an object
	- node instanceof TreeNode
		- node 为 **TreeNode或其子类的实例** 情况下，均返回 true
- return
- while

## package
- import
- package

## primitive
- boolean
- byte
- char
- double
- float
- int
- long
- short

## error
- assert
- try
- catch
- finally
- throw
- throws

## enumeration
- enum

## other
- super
- this
- void

## unused
- const
- goto



# Reference
- 官方
	- [Java Language Keywords](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/_keywords.html)
	- [JavaKeywordReference.pdf](https://docs.oracle.com/cd/E13226_01/workshop/docs81/pdf/files/workshop/JavaKeywordReference.pdf)
	- [List of Java keywords](https://en.wikipedia.org/wiki/List_of_Java_keywords)
- 补充
	- [java native关键字](https://blog.csdn.net/youjianbo_han_87/article/details/2586375)
	- [ava中的strictfp关键字](http://neil-yang.iteye.com/blog/341476)
	- [Java transient关键字使用小记](http://www.cnblogs.com/lanxuezaipiao/p/3369962.html)
	- [**Java并发编程：volatile关键字解析**](https://www.cnblogs.com/dolphin0520/p/3920373.html)
	- [Java 8 默认方法（Default Methods）](http://ebnbin.com/2015/12/20/java-8-default-methods/)
