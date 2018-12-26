---
title: "Java.Thread.Unsafe"
date: "2018-12-23"
categories:
 - "整理"
tags:
 - "Java"
 - "Thread"
toc: true
---


# 描述
- 通过 native 实现
- 实现通过本地内存进行变量的读取、操作
- 并提供以线程低层次控制
- 高并发功能的实现基础
	- 支持 CAS，即 Compare-and-swap
- java 中不推荐
	- sun 包不包含于 Java 标准，**与平台相关**，且随 JDK 版本而变化
	- 但也没有较好的替代方案


# 方法

| 类型     | 方法                                                                  | 补充                                                                                                        |
|----------|-----------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| 构造     |                                                                       |                                                                                                             |
|          | getUnsafe()                                                           | 仅 Java 信息的代码可代码                                                                                    |
| 内存操作 |                                                                       |                                                                                                             |
|          | get**Type**(obj, obj.Filed.offset)                                    | 针对于存放于**java堆**中对象的字段<br>Type: Int,Object,Boolean,Byte,Short,Char,Long,Float,Double            |
|          | `put**Type**(obj, obj.Filed.offset, Type.val)`                        |                                                                                                             |
|          | get**Type**(obj.Filed.offset)                                         | 针对于**C堆**中的值<br>Type: byte,short,char,int,long,float,double                                          |
|          | put**Type**(obj.Filed.offset, type.val)                               |                                                                                                             |
| 内存分配 |                                                                       |                                                                                                             |
|          | allocateMemory(bytes)                                                 | 分配指定大小的虚拟内存                                                                                      |
|          | reallocateMemory(address, bytes)                                      | 重新分配<br>超过历史大小的部分未初始化                                                                      |
|          | setMemory(obj, offset, bytes, value)                                  | 将对象偏移offset后 bytes 位重置为 byte 位<br>byte 常常为0                                                   |
|          | setMemory(address, bytes, value)                                      |                                                                                                             |
|          | `copyMemory(srcObj, orcOffset, dstObj, dstOffset, bytes)`             | 由 srcObj 偏移 srcOffset 处开始拷贝 bytes 位数据至 dstObj 后偏移 dstOffset 处                               |
|          | copyMemory(srcAddress, destAddress, bytes)                            |                                                                                                             |
|          | freeMemory(address)                                                   | 释放指定虚拟内存块                                                                                          |
| 随机访问 |                                                                       |                                                                                                             |
|          | int fieldOffset(Field)                                                | 返回字段的偏移<br>并**截断至 32 位**                                                                        |
|          | long staticFieldOffset(Field)                                         | 返回静态的偏移量                                                                                            |
|          | `long objectFieldOffset(Field)`                                       | 返回给定静态字段的位置，与 staticFieldBase 结合使用                                                          |
|          | Object staticFieldBase(Field)                                         | 返回给定静态字段的位置，与 staticFieldOffset 结合使用                                                        |
|          | ensureClassInitialized(Class)                                         |                                                                                                             |
|          | arrayBaseOffset(Class)                                                |                                                                                                             |
| 指针操作 |                                                                       |                                                                                                             |
|          | getAddress(address)                                                   | 获取内存地址的原生指针                                                                                      |
|          | putAddress(address, *ptr)                                             |                                                                                                             |
|          | addressSize()                                                         | 获取原生指针所在块的大小<br>原生指针由 putAddress 指定                                                      |
|          | pageSize()                                                            | 获取原生内存页的大小<br>大小为 2^n                                                                          |
| 其它操作 |                                                                       |                                                                                                             |
|          | `monitorExit(obj)`                                                    | 解**锁**                                                                                                    |
|          | tryMonitorEnter(obj)                                                  | 尝试加**锁**                                                                                                |
|          | monitorEnter(obj)                                                     | 加**锁**                                                                                                    |
|          | Class defineClass(name, b[], off, len, classloader, protectionDomain) | 不经过安全检查，直接**定义类**                                                                               |
|          | Class defineClass(name, b[], off, len)                                | 类加载器和安全校验延用调用类                                                                                |
|          | `Object allocateInstance(class)`                                      | 直接分配内存**创建实例**，但不调用任何构造函数                                                               |
|          | get**Type**Volatile(obj, offset)                                      | 以 **volatile** 形式获取数据<br>type: Object,Int,Boolean,Byte,Short,Char,Long,Float                         |
|          | put**Type**Volatile(obj, offset, val)                                 |                                                                                                             |
|          | putOrdered**Type**(obj, offset, Object x)                             | 区别于 putTypeVolatile，**不同步通知其它进程**<br>type: Object,Int,Long                                      |
|          | `compareAndSwap**Type**(obj, offset, objExpect, objNew)`              | 当 obj.offset 的值与 objExpect 一致时，更新值为 objNew<br>type: Object,Int,Long                              |
|          | park(isAbsolute, time)                                                | 阻塞当前进程<br>继续执行： unpark() 调用，或当前线程被中断，或超过指定时间                                     |
|          | unpark(thread)                                                        | 唤醒指定进程<br>需先保障线程存活                                                                            |
|          | getLoadAverage(loadavg[], nelems)                                     | 获取系统运行队列中，一定时间内的负载均衡状况<br>nelems 用于检索的样本数，可选[1,2,3]，分别代表1/5/15分钟的采样 |


# 实现

## 源码

```java
// final 限定仅可通过 static 方式构造
private static final Unsafe theUnsafe;

// 私有构造函数
private Unsafe() {}

static {
	// 添加需监测调用者的类、方法
	Reflection.registerMethodsToFilter(Unsafe.class, new String[]{"getUnsafe"});
	theUnsafe = new Unsafe();
}

// 标注要使用 `Reflection.getCallerClass()`，可靠地发现调用者
// 仅 Java 信任的安全代码可以调用
@CallerSensitive
public static Unsafe getUnsafe() {
	Class var0 = Reflection.getCallerClass();
	if (!VM.isSystemDomainLoader(var0.getClassLoader())) {
		throw new SecurityException("Unsafe");
	} else {
		return theUnsafe;
	}
}
```

## 获取实例

```java
// 使用反射方式获取
// 默认仅供 java 信任的代码获取使用
Field theUnsafe = Unsafe.class.getDeclaredField("theUnsafe");
theUnsafe.setAccessible(true);
return (Unsafe) theUnsafe.get(null);
```

## 内存值修改

```java
// 不受 final/ String 等限制
Field field = str.getClass().getDeclaredField("value");
unsafe.putObject(str, unsafe.objectFieldOffset(field), new char[]{'H', 'i'});
```

## 构建对象

```java
// 不受 JVM 安全检查、类的构建函数、初始值限制
PrivateConstructorVo vo = (PrivateConstructorVo) unsafe.allocateInstance(PrivateConstructorVo.class);
```

## 获取对象内存地址

```java
Car car = new Car(100d);
Object[] arr = new Object[]{car};
// 固定的数组头部长度
long offset = unsafe.ARRAY_OBJECT_BASE_OFFSET;
System.out.printf("\tArray[0].address=\t%d\n", unsafe.getLong(arr, offset));
```

# 补充
## 类的内存形式

- 组成

	| 组件                  | 大小<br>32b/64b | 补充                                                                      |
	|-----------------------|-----------------|---------------------------------------------------------------------------|
	| header                | 4B/8B           | 常量<br>`0×00000001`                                                      |
	| klass pointer         | 4B/8B           | 指针<br>类的内存地址                                                      |
	| C++ vtb1 ptr          | 4B/8B           | 指针<br>对应类的虚函数表                                                  |
	| layout_helper         | 4B              | 记录实例浅尺寸                                                            |
	| super check offset    | 4B              |                                                                           |
	| name                  | 4B/8B           |                                                                           |
	| secondary super cache | 4B/8B           |                                                                           |
	| secondary supers      | 4B/8B           |                                                                           |
	| primary supers        | 32B/64B         |                                                                           |
	| java mirror           | 4B/8B           |                                                                           |
	| super                 | 4B/8B           | 父类的内存地址                                                            |
	| first subklass        | 4B/8B           |                                                                           |
	| next sibling          | 4B/8B           |                                                                           |
	| modifier flags        | 4B              | 类的修饰符标志位<br>如 public final= `0×00000001\0×00000010`=  0×00000011 |
	| access flags          | 4B              |                                                                           |

- modifier flags

	| flag      |   Hex   |          Binary |
	|-----------|:-------:|----------------:|
	| public    | 0x 0001 |             `1` |
	| protected | 0x 0002 |            `10` |
	| private   | 0x 0004 |           `100` |
	| static    | 0x 0008 |          `1000` |
	| final     | 0x 0010 |         `10000` |
	| abstrct   | 0x 0400 |  `100 00000000` |
	| strict    | 0x 0800 | `1000 00000000` |

- 示例
	- 图示
		- ![java-memory-layout-1.jpg](http://doc.yqjdcyy.com/ba6274b9-a2c8-4121-b892-26fa33e3c776.jpg)

	- 组成映射

	| 组件                  | 行号   | 大小 | 内容        | 解析                                            |
	|-----------------------|--------|------|-------------|-------------------------------------------------|
	| **header**            | 0x0000 | 4B   | 01 00 00 00 | 固定为 0x 00000001<br>小端排序<br>little-endian |
	| **klass pointer**     |        | 4B   | a8 08 97 38 |                                                 |
	| C++ vtb1 ptr          |        | 4B   | 2c 7a b0 59 |                                                 |
	| layout_helper         |        | 4B   | 18 00 00 00 |                                                 |
	| super check offset    | 0x0010 | 4B   | 28 00 00 00 |                                                 |
	| name                  |        | 4B   | d0 a5 3f 04 |                                                 |
	| secondary super cache |        | 4B   | 00 00 00 00 |                                                 |
	| secondary supers      |        | 4B   | 00 00 f7 37 |                                                 |
	| primary supers        | 0x0020 | 32B  | 00 00 97 38 |                                                 |
	| java mirror           | 0x0040 | 4B   | 40 1f 59 24 |                                                 |
	| super                 |        | 4B   | 00 00 97 38 |                                                 |
	| first subklass        |        | 4B   | c0 4c 10 34 |                                                 |
	| next sibling          | 0x0030 | 4B   | 30 3f 10 34 |                                                 |
	| **modifier flags**    |        | 4B   | 01 00 00 00 | 0x 00000001= public                             |
	| **access flags**      |        | 4B   | 21 00 20 00 |                                                 |


## 尺寸
- 浅尺寸
	- shallow size
	- 对象本身各字段所占用内存大小
		- 若引用字段，仅计算引用字段的长度

- 深尺寸
	- deep size
	- 在浅尺寸的基础上，针对引用字段的长度，为引用对象的浅尺寸大小


## Oops & CompressedOops
- 普通对象指针
	- OOP
	- Ordinary Object Pointer
	- Java 堆中的内存地址
		- 指向 JVM 进程的虚拟地址空间上，一段单一、连续的地址范围
	- 32位系统中，OOP 与机器指针一致

- 压缩的普通对象指针
	- 用 32 位对象偏移指针来表示 64 位字节地址
		- 8 字节对齐+ 偏移
			- CPU 一次性获取的数据为 8bit
	- 可通过 `-XX:-UseCompressedOops` 关闭


## CAS

- 场景
	- 三个线程分别运行如下代码

		```java
		int ori= get();					// 1
		int dst= ori+ 2;				// 2	
		if(compareAndSet(ori, dst)) return dst;		// 3						
		```

	- 流程
		- Thread-1 运行到阶段1 时切换
			- ori= 3
		- Thread-2 阶段3 完成
			- ori= 3
			- dst= 3+ 2= 5, return
		- Thread-1 恢复，并继续执行
			- `compare(ori= 3 <> cpu.ori= 5) and not set`
			- ori= get()= 5
			- if(compare(5, cpu.ori)) set cpu.ori= 5+ 2= 7

- 缺点
	-  ABA
		- Thread-1.get()= A
		- Thread-2.compareAndSet()= B
		- Thread-3.compareAndSet()= A
		- Thread-1.compareAndSet()
			- 实际已经修改过，但无法判断出来
	- 解决办法
		- `AtomicStampedReference`



# 参考
- [sun.misc.Unsafe的理解](http://www.cnblogs.com/chenpi/p/5389254.html)
- [深入解析Java反射-invoke方法](https://www.sczyh30.com/posts/Java/java-reflection-2/#%E6%9C%AC%E7%AF%87%E6%80%BB%E7%BB%93)
- [What does the sun.reflect.CallerSensitive annotation mean?](https://stackoverflow.com/questions/22626808/what-does-the-sun-reflect-callersensitive-annotation-mean)
- [JEP 176: Mechanical Checking of Caller-Sensitive Methods](http://openjdk.java.net/jeps/176)
	- @CallerSensitive 注解的作用
- [为什么不建议调用sun包，如何通过其他方法确定调用者](http://www.itzhai.com/get-invoker-by-stacktrace-and-getcallerclass.html)
	- sun 包不包含于 Java 标准，与平台相关，且随 JDK 版本而变化
- [sun.misc.Unsafe 的使用](https://fujinbing.iteye.com/blog/695923)
- [sun.misc.unsafe类的使用](https://blog.csdn.net/fenglibing/article/details/17138079)
- [sun.misc.Unsafe.java](http://www.docjar.com/html/api/sun/misc/Unsafe.java.html)
	- 源码！
- [Memory values with java](https://stackoverflow.com/questions/13710262/memory-values-with-java)
	- 解释 `native pointer`
		- 使用 32-bit 引用来指定 64-bit JVM 堆地址
- [What is a native pointer and returnAddress?](https://stackoverflow.com/questions/38672839/what-is-a-native-pointer-and-returnaddress)
- [如何使用Unsafe操作内存中的Java类和对象—Part1](http://www.importnew.com/7844.html)
- [如何使用Unsafe操作内存中的Java类和对象—Part2](http://www.importnew.com/8488.html)
- [如何使用Unsafe操作内存中的Java类和对象—Part3](http://www.importnew.com/8494.html)
- [如何使用Unsafe操作内存中的Java类和对象—Part4](http://www.importnew.com/8514.html)
- [JUC中Atomic class之lazySet的一点疑惑](http://ifeve.com/juc-atomic-class-lazyset-que/)
	- set 与 lazyset 的差异在于前者使用 vilatile 于线程可见性上添加了 StoreStore 和 StoreLoad 内存屏障，而后者未添加 `StoreLoad` 这`内存屏障中最消耗性能`的
- [java魔法之unsafe](https://leokongwq.github.io/2016/12/31/java-magic-unsafe.html)
	- 自己测试的场景 long/ lock/ atomicLong/ unsafe 情况与博主情况不一致

		```java
		NormalCounter(50000)* 	10000=	330691887 in	4625
		LockCounter(50000)* 	10000=	500000000 in	13848
		AtomicCounter(50000)* 	10000=	500000000 in	14438
		UnsafeCounter(50000)* 	10000=	477275360 in	98691
		```