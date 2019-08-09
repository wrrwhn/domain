---
title: "Maven.Lombok 找不到符号"
date: "2019-08-09"
categories:
 - "整理"
tags:
 - "Maven"
 - "Lombok"
toc: true
---


# Lombok 未生效，提示找不到符号

## 异常信息

```
[ERROR] 位置: 类型为...IndicatorConfig的变量 cfg
[ERROR] .../IndicatorConfigGeneratorImpl.java:[69,53] 找不到符号
[ERROR] 符号:   方法 getEsDaySum()
[ERROR] 位置: 类型为com.ucarinc.wukong.monitor.indicator.config.bo.IndicatorConfig的变量 cfg
[ERROR] .../IndicatorRpcHandler.java:[59,9] 找不到符号
[ERROR] 符号:   变量 log
```

## 异常日志
- 务必关注日志
    - 个人没关注，觉得没问题，导致钻了好一阵的牛角尖

```
[WARNING] .../Tuple2BroadcastProcessFunction.java:[25,8] lombok.javac.apt.LombokProcessor could not be initialized. 
Lombok will not run during this compilation: 
    java.lang.ClassCastException: 
        com.sun.tools.javac.processing.JavacProcessingEnvironment cannot be cast to com.sun.tools.javac.processing.JavacProcessingEnvironment
```


## 原因分析
观看异常日志，发现比较特别的异常是 `ClassCastException`，怀疑有同路径同类名文件
经类名搜索发现，在公司的 `base-framework` 中 `jdk-tools` 有重名类！

！类加载顺序 

## 解决办法
- 在 `pom.xml` 将 `jdk-tools` 排除出去

## 补充
- 最常出现的情况，是由于项目未允许注解执行
    - 解决办法即将项目配置允许执行注解
        - 配置路径为 `/Settings/Build, Execution, Deployment/ Compiler/ Annotation Processors`
        - 选择项目，开启右侧的 `Enable annotation processing` 配置