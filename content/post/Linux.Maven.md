+++
date = "2017-08-17T22:07:46+08:00"
title = "Linux.Maven"
draft = false
tags = ["整理","Linux","Maven"]
share = true
+++

[TOC]

## 参考
- [Installing Apache Maven](https://maven.apache.org/install.html)
- [Maven - 环境配置](http://wiki.jikexueyuan.com/project/maven/environment-setup.html)
- [apache-maven-3.5.0-bin.zip](http://mirrors.hust.edu.cn/apache/maven/maven-3/3.5.0/binaries/apache-maven-3.5.0-bin.zip)
- [apache-maven-3.5.0-bin.tar.gz](http://mirrors.hust.edu.cn/apache/maven/maven-3/3.5.0/binaries/apache-maven-3.5.0-bin.tar.gz)

## 流程
### Java.config
- check $JAVA_HOME

### Maven.config
- UNZIP
    - /data/soft/apache-maven-3.5.0
- EXPORT.ADD
    - /etc/profile
        ```
        export M2_HOME=/data/soft/apache-maven-3.5.0
        export M2=$M2_HOME/bin
        export MAVEN_OPTS='-Xms256m -Xmx512m'
        export PATH=${PATH}:${M2}
        ```
- EXPORT.SAVE
    - `source /etc/profile`
- CHECK
    - `mvn -v`
- MAVEN.CONFIG
    - `/data/soft/apache-maven-3.5.0/conf/settings.xml`
        - `<localRepository>/data/soft/apache-maven-3.5.0/repo</localRepository>`

## 常用指令
- `clean`
- `install`
- `-Dmaven.test.skip=true`
- `-Dmaven.repo.local=/data/.m2`
- `-X`
    - 显示 Debbug 信息
