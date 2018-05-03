+++
date = "2018-05-03T23:20:00+08:00"
title = "Java.Tools.JINFO"
draft = false
tags = ["整理","Java","Tools"]
share = true
+++

[TOC]

# JINFO
## 作用
- 输出进程、Core 文件或远程 Debug 服务器配置信息
    - Java 系统参数
    - 命令行参数
- 运行于64位虚拟机上时，需指定 `-J-d64`
- Java **1.8** 版本中**不支持**

## 调用
- `jinfo [option] <pid>`
- 参数
    - `-flag <name>`         
        - to print the value of the named VM flag
    - `-flag [+|-]<name>`    
        - to enable or disable the named VM flag
    - `-flag <name>=<value>` 
        - to set the named VM flag to the given value
    - `-flags`               
        - to print VM flags

## 示例
    - `jinfo pid`
        ```
        Caused by: sun.jvm.hotspot.runtime.VMVersionMismatchException: Supported versions are 24.60-b09. Target VM is 25.25-b02
        ```

# Reference        
- [Java命令学习系列（六）——jinfo](http://www.hollischuang.com/archives/1094)
- [jinfo](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jinfo.html)