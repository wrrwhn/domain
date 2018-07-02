---
title: "Java.Tools.Javap"
date: "2018-06-06"
categories:
 - "整理"
tags:
 - "Java"
 - "tools"
toc: true
---


# JAVAP
## 作用
- 对 class 文件进行反编译
- 查看 java 编译器生成的字节码

## 调用
- `javap [options] classfile`
- 参数
    - `-help| --help| -?`
    	- 打印 `JAVAP` 命令的帮助文档
    - `-version`
    	- 打印发布版本信息
    - `-l`
    	- 打印类中变量和方法及其参数列表
    - `-public`
    	- 仅显示公有类型的类和成员
    - `-protected`
    	- 仅显示保护或公有类型的类和成员
    - `-private| -p`
    	- 显示所有的类和成员
    - `-Joption`
    	- 显示 JVM 相关的特定参数
    - `-s`
    	- 显示内部类型签名
    - `-sysinfo`
    	- 显示该类执行时的相关系统信息，如路径、大小、日期和 MD5 签名值
    - `-constants`
    	- 显示最终的静态常量
    - `-c`
    	- 为每个方法打印组成的 Java 字节码指令
    - `-verbose`
    	- 打印堆大小、局部变量的数量、方法的参数

## 示例
- `javap CourseVo.class`
    ```
    Compiled from "CourseVo.java"
    public class com.yunkai.training.portal.course.course.vo.CourseVo {
      public com.yunkai.training.portal.course.course.vo.CourseVo();
      public java.lang.String getOrgName();
      public void setOrgName(java.lang.String);
    }
    ```

- `javap -c CourseVo.class`
    ```
    Compiled from "CourseVo.java"
    public class com.yunkai.training.portal.course.course.vo.CourseVo {
      public com.yunkai.training.portal.course.course.vo.CourseVo();
        Code:
           0: aload_0
           1: invokespecial #1                  // Method java/lang/Object."<init>":()V
           4: return

      public java.lang.String getOrgName();
        Code:
           0: aload_0
           1: getfield      #23                 // Field orgName:Ljava/lang/String;
           4: areturn

      public void setOrgName(java.lang.String);
        Code:
           0: aload_0
           1: aload_1
           2: putfield      #23                 // Field orgName:Ljava/lang/String;
           5: return
    }
    ```

- `javap CourseController.class`
    ```
    Compiled from "CourseController.java"
    public class com.yunkai.training.portal.course.course.controller.CourseController {
      com.yunkai.training.portal.course.course.service.CourseService courseService;
      public java.util.Map<java.lang.String, java.lang.Object> configLimitation(java.lang.Long, java.lang.Long);
    }
    ```

- `javap -c CourseController.class`
    ```
    Compiled from "CourseController.java"
    public class com.yunkai.training.portal.course.course.controller.CourseController {
      com.yunkai.training.portal.course.course.service.CourseService courseService;
      public java.util.Map<java.lang.String, java.lang.Object> configLimitation(java.lang.Long, java.lang.Long);
        Code:
           0: aload_0
           1: getfield      #12                 // Field courseService:Lcom/yunkai/training/portal/course/course/service/CourseService;
           4: aload_1
           5: aload_2
           6: invokevirtual #45                 // Method com/yunkai/training/portal/course/course/service/CourseService.limitation:(Ljava/lang/Long;Ljava/lang/Long;)Ljava/util/Map;
           9: areturn
    }
    ```

- `javap -s CourseController.class`
	```
	public void remove(java.lang.Long, java.lang.Long);
	    descriptor: (Ljava/lang/Long;Ljava/lang/Long;)V
	```

- `javap -sysinfo CourseController.class`
	```
	Classfile /D:/work/git/yk/java/mgmt_server/code/basic-portal/target/classes/com/yunkai/training/portal/channel/circulation/course/controller/CirculationCourseController.class
	  Last modified 2018-6-5; size 9352 bytes
	  MD5 checksum f41f036052304f6eb835711ccb32e12d
	  Compiled from "CirculationCourseController.java"
	public class com.yunkai.training.portal.channel.circulation.course.controller.CirculationCourseController {
	  com.yunkai.training.commons.course.course.service.CourseService courseService;
	```


# Reference
- 官方
    - [javap](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/javap.html)
- 其它
    - [Java命令学习系列（七）——javap](http://www.hollischuang.com/archives/1107)