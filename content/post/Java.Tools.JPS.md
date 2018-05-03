+++
date = "2018-05-03T23:20:00+08:00"
title = "Java.Tools.JPS"
draft = false
tags = ["整理","Java","Tools"]
share = true
+++

[TOC]

# JPS
- 作用
    - 显示当前用户本地 JAVA 进程及进程号
- 机制
    - java 程序启动后，会在 `java.io.tmpdir` 指定临时目录下，生成名称类似于 `hsperfdata_User` 的文件夹，其中个别文件的名字就是 java 进程的 `pid`
    - 示例
        - `appuser` 用户
        - `ll /tmp/hsperfdata_appuser/`
            ```
            total 928
            -rw------- 1 appuser appuser 32768 May  2 22:17 11337
            -rw------- 1 appuser appuser 32768 May  2 22:17 11489
            ```
- 调用
    - `jps [-q] [-mlvV] [<hostid>]`
    - 参数
        - `-q`
            - 仅显示 `pid` 值
        - `-m`
            - 显示调用时 **main** 函数收到的启动**参数**
        - `-l`
            - 显示启动类的进程 ID 和完整路径名
        - `-v`
            - 显示调用 **JVM** 时的相关**参数**
    - 示例
        - `/usr/java/jdk1.7.0_60/bin/jps -m`
            ```
            26324 Maven31Main /data/jenkins/tools/hudson.tasks.Maven_MavenInstallation/_usr_local_maven /var/cache/jenkins/war/WEB-INF/lib/remoting-2.53.2.jar /data/jenkins/plugins/maven-plugin/WEB-INF/lib/maven31-interceptor-1.5.jar /data/jenkins/plugins/maven-plugin/WEB-INF/lib/maven3-interceptor-commons-1.5.jar 52930
            11489 Kafka config/server.properties
            23692 Bootstrap start
            ```
        - `/usr/java/jdk1.7.0_60/bin/jps -v`
            ```
            9107 Bootstrap -Djava.util.logging.config.file=/data/service/webapps/basic-operation-test/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Dtomcat.instance.name=basic-operation-test -Dspring.profiles.active=test -Dspring.cloud.config.uri=http://localhost:29011/config-server -Xms600m -Xmx600m -XX:+UseConcMarkSweepGC -XX:+UnlockDiagnosticVMOptions -XX:+PrintGCDetails -XX:+PrintClassHistogramBeforeFullGC -XX:+PrintClassHistogramAfterFullGC -XX:+HeapDumpOnOutOfMemoryError -Djava.endorsed.dirs=/data/service/tomcat/endorsed -Dcatalina.base=/data/service/webapps/basic-operation-test -Dcatalina.home=/data/service/tomcat -Djava.io.tmpdir=/data/service/webapps/basic-operation-test/temp
            17888 Elasticsearch -Xms256m -Xmx2g -Djava.awt.headless=true -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseCMSInitiatingOccupancyOnly -XX:+HeapDumpOnOutOfMemoryError -XX:+DisableExplicitGC -Dfile.encoding=UTF-8 -Delasticsearch -Des.path.home=/data/elasticsearch-test
            26324 Maven31Main
            ```


# Reference
- [Java命令学习系列（一）——Jps](http://www.hollischuang.com/archives/105)
- [jps](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jps.html)