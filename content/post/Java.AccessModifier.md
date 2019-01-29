---
title: "Java.AccessModifier"
date: "2019-01-10"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---


# 作用域

|  修饰符   | 类内部 | 同包 | 子类 | 跨包 |
|-----------|--------|------|------|------|
| public    | Y      | Y    | Y    | Y    |
| protected | Y      | Y    | Y    | X    |
| default   | Y      | Y    | X    | X    |
| private   | Y      | X    | X    | X    |


# 示例

## 文件结构

```
- access
	- Access.java
	- AccessLocal.java
	- other
		- AccessSub.java
		- OutAcess.java
```

## 调用情况

|           | Access<br> package access |             AccessLocal<br>package access             | AccessSub<br>extends Access | OutAccess<br>package access.other |
|-----------|---------------------------|-------------------------------------------------------|-----------------------------|-----------------------------------|
| public    | Y                         | Y                                                     | Y                           | Y                                 |
| protected | Y                         | Y                                                     | Y                           | X                                 |
| default   | Y                         | Y                                                     | X                           | X                                 |
| private   | Y                         | X<br>'privateMethod()' has private access in 'Access' | X                           | X                                 |


# 补充
- 同包的概念，是指java 文件 package 的目录
	- 如 access 与 access.other 理解上是同包，但从设置上已然分开
- Java 的访问控制集中于**编译**时
- 通过**反射**可访问任意类中的成员、方法



# 参考
- [Java中4种访问修饰符](https://blog.csdn.net/mingjie1212/article/details/50539188)


