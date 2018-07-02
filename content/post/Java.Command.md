---
title: "Java.Command"
date: "2017-08-09"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---

## Javac
- 作用
    + 将 *.java 源代码转换为 *.class 格式文件
- 参考
   + [Javac编译原理](http://www.cnblogs.com/java-zhao/p/5194064.html)
- 事例
    + 目录
        * D:\workspace\Knowledge_Structure\jni\JNIProxy.java
    + 执行语句
        * D:\workspace\Knowledge_Structure\jni> javac JNIProxy.java

## Javah
- 作用
    + 生成类中所需的 JNI 头文件
- 参考
    + [用javah 导出类的头文件， 常见的错误及正确的使用方法](http://blog.csdn.net/hejinjing_tom_com/article/details/8125648)
- 事例
    + 目录
        * D:\workspace\work4Git\Knowledge_Structure\00.语言\Java\代码\java-study\src\main\java\ `com\yao\study\java\jni\JNIProxy.java`
    + 类定义
        * package `com.yao.study.java.jni`;
        * public class JNIProxy{...}
    + 执行语句
        * `D:\workspace\work4Git\Knowledge_Structure\00.语言\Java\代码\java-study\src\main\java`> javah -o jni_msg.h `com.yao.study.java.jni.JNIProxy`
