---
title: "Java.JVM.对象模型"
date: "2018-12-27"
categories:
 - "整理"
tags:
 - "Java"
 - "JVM"
toc: true
---


# OOP-Klass-Model

## 设计

### 结构

| Level | Level         | Level  | Level     | 描述                                                          |
|-------|---------------|--------|-----------|---------------------------------------------------------------|
| OOP   |               |        |           | 普通对象类型<br>Ordinary Object Pointer                       |
|       | InstanceOop   |        |           |                                                               |
|       |               | Header |           | 对象头                                                        |
|       |               |        | Mark Word | 记录锁、GC分代等相关信息                                       |
|       |               |        | *Klass    | `InstanceKlass` 的指针                                          |
|       |               | Data   |           | 实例数据                                                      |
| Klass |               |        |           | 用于描述 Java 类的 C++ 对等结构<br>解决 Java 对象的分发       |
|       | InstanceKlass |        |           | 加载 class 文件时，在方法区创建<br>内容包括常量池、字段和方法等 |


### 原因
- C++
	- 由虚函数表和数据组成
	- 虚函数表按声明顺序记录所有的虚函数
		- 编译时，虚函数表的信息将由子类重写的方法替换
- Java
	- 避免每个对象都包含虚函数表
	- 将这部分重复的信息保存在统一的表示，`类`，上


## OOP

### 定义
|   表    |                         字段                         |              作用              |
|---------|------------------------------------------------------|--------------------------------|
| oopDesc |                                                      | 用于代码不同类型在 VM 中的定义 |
|         | volatile markOop  _mark                              | 上例中的 `Mark Word`           |
|         | union _metadata {wideKlassOop; narrowOop;} _metadata | 上例中的 `*Klass`              |
|         | friend class VMStructs                               |                                |
|         | static BarrierSet* _bs;                              | 内存屏障设置                   |


### 继承

|         类型         |     继承     |           作用          |
|----------------------|--------------|-------------------------|
| Oop                  |              | 基类                    |
| InstanceOop          |              | 类实例                  |
| ArrayOop             |              | 数据抽象基类            |
|                      | ObjArrayOop  | 对象数组                |
|                      | TypeArrayOop | 类型数组                |
| MarkOop              |              | 对象头                  |
| ConstantPoolOop      |              | 常量池                  |
| ConstantPoolCacheOop |              | 常量池缓冲区            |
| MethodOop            |              | 方法                    |
| KlassOop             |              | 与 Java 类对等的 C++ 类 |
| ConstMethodOop       |              | 静态方法                |
| MethodDataOop        |              | 性能相关                |


## Klass

### 定义

|      表       |                  字段                  |    作用    |
|---------------|----------------------------------------|------------|
| InstanceKlass |                                        |            |
|               | objArrayOop     _methods               | 方法列表   |
|               | typeArrayOop    _method_ordering       | 方法顺序   |
|               | objArrayOop     _local_interfaces      | 实现接口   |
|               | objArrayOop     _transitive_interfaces | 继承的接口 |
|               | typeArrayOop    _fields                | 域<br>字段 |
|               | constantPoolOop _constants             | 常量       |
|               | oop             _class_loader          | 类加载器   |
|               | oop             _protection_domain     | 保护哉     |
	

### 继承
|          类型          |         继承        |                作用               |
|------------------------|---------------------|-----------------------------------|
| Klass_vtbl             |                     |                                   |
| Klass                  |                     |                                   |
| InstanceKlass          |                     | 描述 Java 类                      |
|                        | InstanceMirrorKlass | 描述 java.lang.class              |
|                        | InstanceRefKlass    | 代表 java.lang.ref.Reference 子类 |
| KlassKlass             |                     | 描述 instanceKlass 的 Klass       |
|                        | InstanceKlassKlass  |                                   |
|                        | ArrayKlassKlass     |                                   |
|                        | ObjArrayKlassKlass  |                                   |
|                        | TypeArrayKlassKlass |                                   |
| ArrayKlass             |                     |                                   |
|                        | ObjArrayKlass       |                                   |
|                        | TypeArrayKlass      |                                   |
| ConstantPoolKlass      |                     |                                   |
| ConstantPoolCacheKlass |                     |                                   |
| ConstMethodKlass       |                     |                                   |
| MethodKlass            |                     |                                   |
| MethodDataKlass        |                     |                                   |
| CompliedICholderKlass  |                     |                                   |

### 引用

- 建立在 Klass 也是一个 Oop 的基础上
- 引用路径
	- Instance/ OopDesc
	- InstanceKlass
	- InstanceKlassKlass
	- KlassKlass
	
- 引用图示
	- ![JVM-Klass-OOPS.png](http://doc.yqjdcyy.com/c3dec71d-b24a-46df-ba99-3e719ee3108d.png)




# 示例

## 代码

```java
class Model
{
    public static int a = 1;
    public int b;

    public Model(int b) {
        this.b = b;
    }
}

public static void main(String[] args) {
    int c = 10;
    Model modelA = new Model(2);
    Model modelB = new Model(3);
}
```

## 内存结构的图示
- ![JVM-OOP-Klass.jpg](http://doc.yqjdcyy.com/0a4aca32-f863-47d3-8dfd-e9d4ec7f60ef.jpg)


# 补充
## 功能分发
- Oops 需要使用虚函数时
	- 在 Klass 中包含有 VTable，因此可包含虚函数
	- 定义 Oops 类上的虚函数
		- 于相关的 Klass 上定义
		- Oops 上定义内联的非虚函数，并委托给 klass 是定义的虚函数

## JDK1.8 后的结构变化
- 1.7-
	- 获得对象，需要知道对套大小时，则需要通过 `_klass` 获得得 Klass 对象来获取
	- Klass 也作为 Oop 对象存储，便于 GC 操作
	- 元数据结构

		```java
		union _metadata {
			wideKlassOop    _klass;
			narrowOop       _compressed_klass;
		} _metadata;
		```

- 1.8+
	- 移除 `*KlassKlass` 类型
		- 元数据被移动到本地主存中
	- 元数据结构

		```java
		union _metadata {
			Klass*      _klass;
			narrowKlass _compressed_klass;
		} _metadata;
		```

## instanceKlass 层级

|                字段               |                  描述                  |
|-----------------------------------|----------------------------------------|
| header                            | klassOop                               |
| klass pointer                     | klassOop                               |
| C++ vtbl pointer                  | Klass                                  |
| subtype cache                     | Klass                                  |
| instance size                     | Klass                                  |
| java mirror                       | Klass                                  |
| super                             | Klass                                  |
| access_flags                      | Klass                                  |
| name                              | Klass                                  |
| first subklass                    | Klass                                  |
| next sibling                      | Klass                                  |
| array klasses                     |                                        |
| methods                           |                                        |
| local interfaces                  |                                        |
| transitive interfaces             |                                        |
| number of implementors            |                                        |
| implementors                      | klassOop[2                             |
| fields                            |                                        |
| constants                         |                                        |
| class loader                      |                                        |
| protection domain                 |                                        |
| signers                           |                                        |
| source file name                  |                                        |
| inner classes                     |                                        |
| static field size                 |                                        |
| nonstatic field size              |                                        |
| static oop fields size            |                                        |
| nonstatic oop maps size           |                                        |
| has finalize method               |                                        |
| deoptimization mark bit           |                                        |
| initialization state              |                                        |
| initializing thread               |                                        |
| Java vtable length                |                                        |
| oop map cache (stack maps)        |                                        |
| EMBEDDED Java vtable              | size in words = vtable_len             |
| EMBEDDED nonstatic oop-map blocks | size in words = nonstatic_oop_map_size |


# 参考
- [对象在jvm中的表示：OOP-Klass模型](https://blog.csdn.net/linxdcn/article/details/73287490)
- [Java的对象模型](https://www.hollischuang.com/archives/1910)
- [oop.hpp](https://github.com/openjdk-mirror/jdk7u-hotspot/blob/50bdefc3afe944ca74c3093e7448d6b889cd20d1/src/share/vm/oops/oop.hpp)
	- 源码
- [instanceKlass.hpp](https://github.com/openjdk-mirror/jdk7u-hotspot/blob/50bdefc3afe944ca74c3093e7448d6b889cd20d1/src/share/vm/oops/instanceKlass.hpp)
	- 源码
- [JVM内存结构 VS Java内存模型 VS Java对象模型](https://www.hollischuang.com/archives/2509)
	- 主要区分三层结构
- [JEP 122: Remove the Permanent Generation](http://openjdk.java.net/jeps/122)
	- 移除永生代的相关操作、原因
- [HotSpotVM 对象机制实现浅析](https://blog.csdn.net/kisimple/article/details/44632443)
	- 借用 HotSpot SA 工具了解其实际结构
- [深入探究 JVM | klass-oop对象模型研究](https://www.sczyh30.com/posts/Java/jvm-klass-oop/)
- [Java对象模型](https://www.jianshu.com/p/e2a1cbc4ea13)
	- 补充 handle 类，和 OOP 等相关命名由来
- []()