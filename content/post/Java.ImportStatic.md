---
title: "Java.ImportStatic"
date: "2018-05-23"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---


# DESCRIBE
## 发布
- JDK 1.5 新特性
## 对比
- `import`
	- 从包中引入类，允许不添加类前缀的方式进行调用
		- `imports classes from packages, allowing them to be used without package qualification`
- `static import`
	- 声明从类中引入静态成员，以允许它们在该类中直接使用，而不需要添加类前缀
		- `declaration imports static members from classes, allowing them to be used without class qualification.`

# 使用
- `import class.[*|method]`

# 示例
## Origin
```
double r = Math.cos(Math.PI * theta);
```

## Static Import
```
import static java.lang.Math.*;
double r = cos(PI * theta);
```

# 时机
## 态度
- **保守**地使用
- 仅当需频繁的访问一两个类中的静态成员

## 缺点
- 增加可读复杂度
- 增加维护成本


# REFERENCE
## 官方
- [Static Import](https://docs.oracle.com/javase/1.5.0/docs/guide/language/static-import.html)

## 对比
- [import static和import的区别](http://blog.sina.com.cn/s/blog_625651900100kwul.html)
- [java中import static和import的区别](https://www.jianshu.com/p/d4468cdd1ef3)
