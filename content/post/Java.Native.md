---
title: "Java.Native"
date: "2017-11-16"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---


### 参考
- http://blog.csdn.net/xw13106209/article/details/6989415
- http://www.iteye.com/topic/304594

### 概念
- Jni
    + sun 提供
    + 支持通过与 c/c++ 交互调用系统的相关技术
- Jawin
    + sourceforge 提供基于 Jni 的应用库
    + 支持调用 com 对象，和win32-dll 动态链接库的方法
- Jacob
    + sourceforge 提供
    + 提供调用 microsoft 的 com 对象方法
- 补充
    + ![9c981710-b709-11e6-a8b8-0071cc916200.png](/9c981710-b709-11e6-a8b8-0071cc916200.png)
    + ![22ce8706-b70a-11e6-894c-0071cc916200.png](/22ce8706-b70a-11e6-894c-0071cc916200.png)
    + ![e503f014-b70e-11e6-9aca-0071cc916200.png](/e503f014-b70e-11e6-9aca-0071cc916200.png)
    + ![3881d6ca-b70f-11e6-87ee-0071cc916200.png](/3881d6ca-b70f-11e6-87ee-0071cc916200.png)

### 开发流程
- Java 调用类
- javah 生成 c/c++ 原生函数头文件
- c/c++ 中实现原生函数
- 将项目依赖的所有*原生库和资源*加入至 java 项目的 `java.library.path`
- 生成 java 程序
- 发布 java 应用和 dll 库
