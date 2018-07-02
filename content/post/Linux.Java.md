---
title: "Linux.Java"
date: "2018-06-20"
categories:
 - "整理"
tags:
 - "Linux"
 - "Java"
toc: true
---


## 参考

- [Java SE](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
- [CentOS 7 下安装jdk1.8](https://blog.argcv.com/articles/3155.c)
- [Download Oracle Java JRE & JDK using a script](https://ivan-site.com/2012/05/download-oracle-java-jre-jdk-using-a-script/)


## 安装
- 查询
	- [Java SE Downloads](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
	- 使用 Oracle 账号登录
- 下载
	- 获取下载链接
		- [jdk-8u171-linux-x64.rpm](http://download.oracle.com/otn-pub/java/jdk/8u171-b11/512cd62ec5174c3487ac17c61aaa89e8/jdk-8u171-linux-x64.rpm)
	- F12 获取 Cookie 「gpw_e24」「oraclelicense」
	- 拼链接调用
		- `wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2Ftechnetwork%2Fjava%2Fjavase%2Fdownloads%2Fjdk8-downloads-2133151.html;oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u171-b11/512cd62ec5174c3487ac17c61aaa89e8/jdk-8u171-linux-x64.rpm"`
- 安装
    - `sudo yum localinstall jdk-8u171-linux-x64.rpm -y`
- 检查
    - `java -version`