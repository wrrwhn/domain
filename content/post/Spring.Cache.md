---
title: "Spring.Cache"
date: "2016-12-08"
categories:
 - "整理"
tags:
 - "Spring"
 - "Cache"
toc: true
---


## 参考链接
- http://www.ibm.com/developerworks/cn/opensource/os-cn-spring-cache/

## 特点
- 通过少量的配置 annotation 注释即可使得既有代码支持缓存
- 支持开箱即用 Out-Of-The-Box，即不用安装和部署额外第三方组件即可使用缓存
- 支持 Spring Express Language，能使用对象的任何属性或者方法来定义缓存的 key 和 condition
- 支持 AspectJ，并通过其实现任何方法的缓存支持
- 支持自定义 key和自定义缓存管理者，具有相当的灵活性和扩展

## 注释标签
- @Cacheable(value="缓存名称，如a/{b, c}"[, key="可使用SqEL获取方法参数如#userName，其中缺少按照方法所有参数进行组合"] [, condition="可使用SqEL表达式编写，为true时进行缓存"])
- @CachEvict(value="缓存名称，如a/{b, c}" [, key="可使用SqEL获取方法参数如#userName，其中缺少按照方法所有参数进行组合"] [, condition="可使用SqEL表达式编写，为true时进行缓存"] [, allEntries="true时表示清空所有缓存内容"] [, beforeInvocation="true时表示在方法执行前清空"])
- @CachePut调用方式同@Cacheable，区别于每次都会触发真实方法调用

## 基本原理
- 关键：Spring AOP
- 过程：
    - 原始代码：客户端通过直接作用于类对象的方法进行直接引用
    - 调整实现：客户端申请调用时，所拥有的为代码的引用。proxy控制实际pojo.foo()方法的入参和返回值
    - ![6d7850fe-bda2-11e6-ab18-0071cc916200.png](http://img.yqjdcyy.com/6d7850fe-bda2-11e6-ab18-0071cc916200.png)
