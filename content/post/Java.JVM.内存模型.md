---
title: "Java.JVM.内存模型"
date: "2018-12-26"
categories:
 - "整理"
tags:
 - "Java"
 - "JVM"
toc: true
---


# 前置

## CPU 和缓存一致性
- 发展
	- CPU 的执行速度远超内存读取
	- 通过增加更快的多级`缓存`以缓解
		- 运行时将所需要数据复制至 CPU 的调整缓存，待运算完成后再刷新至主存
		- 通过增加多级缓存来缓解速度的不匹配
- 场景
	- `多线程`时，各线程若持有对同一对象的缓存，则可能导致缓存内容不一致

## 处理器优化
- `处理器`对输入的代码进行`乱序`执行
	- 保障处理器充分被利用

## 指令重振
- JIT 对代码进行指令重排
	- JIT= JVM.即时编译器


# 特性
## 原子性
- CPU 操作不可中断，要么执行完成，要么不执行

## 可见性
- 多个线程访问同一变量，其中一个线程修改了变量值，其它线程能立即收到通知

## 有序性
- 程序执行顺序，同代码先后顺序

# 内存模型
## 作用
- 内存模型定义了多线程程序读写操作的行为规范，保障了共享内存的三个特性

## 定义
- JMM
- Java Memory Model
- 屏蔽硬件和系统的访问差异，保证程序于各平台下对内存的的访问琶的机制和规范
	- 进程间的通信，通过主存和线程间变量的传递同步进行

## 方式
- 限制处理器优化
- 内存屏障


# 实现
## 原子性
- synchronized
	- Unsafe.monitorenter/ monitorexit

## 可见性
- volatile
	- 所修饰变量在使用前，均要从主存中刷新	

## 有序性
- synchronized
	- 保证同一时刻只允许一线程操作
- volatile	
	- 禁止指令重排
		- store-sotre & store-load


# 补充
## happens-before
- 作用
	- 传递数据的可见性
		- monitor 的后拥有者，拥有对前拥有者操作数据的可见性
		- volatile 的后操作者，拥有对前操作的操作动作的可见性
		- hb(a,b) & hb(b,c)= hb(a,cgg)
	- 判断数据是否存在竞争、线程是否安全

# 参考
- [再有人问你Java内存模型是什么，就把这篇文章发给他。](https://www.hollischuang.com/archives/2550)		
- [JSR-133：JavaTM内存模型与线程规范](http://ifeve.com/wp-content/uploads/2014/03/JSR133%E4%B8%AD%E6%96%87%E7%89%881.pdf)		
- [happens-before俗解](http://ifeve.com/easy-happens-before/)		
- [Java内存模型之happens-before](http://cmsblogs.com/?p=2102)		
