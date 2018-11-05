---
title: "IDEA.Java.Version"
date: "2018-04-20"
categories:
 - "整理"
tags:
 - "Java"
 - "IDEA"
toc: true
---


# Error
- Run
	- `Error:java: Compilation failed: internal java compiler error`
- Install
	- `Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.1:compile (default-compile) on project thinking-in-java: Compilation failure: Compilation failure:`
	- `xxxx.java:[26,56] -source 1.5 中不支持 diamond 运算符`

# Fix
## IDEA.Setting
- Project Structure
	- 快捷键
		- `Ctrl+ Alt+ Shift+ S`
	- 设置
		- project Setting/ Porject
			- Project SDK-> 1.8
			- Project language level-> 8 - Lambdas, type annotation etc.
		- project Setting/ Modules
			- choice your error modules
			- Sources/ Language level-> 8 - Lambdas, type annotation etc.
			- Dependencies/ Module SDK-> 1.8
	- 截图
		- ![1.1.png](http://doc.yqjdcyy.com/69dbd275-b2f5-4a46-8c5a-6533f072092b.png)
		- ![1.2.png](http://doc.yqjdcyy.com/1b271cf6-6892-45bd-8ebb-637d0268b1e7.png)
		- ![1.3.png](http://doc.yqjdcyy.com/49c887cd-49f1-4823-9519-f92fbdce4208.png)


- Setting
	- 快捷键
		- `Ctrl+ Alt+ S`
	- 设置
		- Build,Execution,Deployment/ Compiler/ Java Compiler
			- Project bytecode version-> 1.8
			- Per-module bytecode version
				- choice your project
					- Target bytecodes version-> 1.8
	- 截图
		- ![2.1.png](http://doc.yqjdcyy.com/12d5e113-12dd-47d1-95aa-7af7feda4b1f.png)


## Maven.Setting
- plugin
	```
	<build>
	    <plugins>
	        <plugin>
	            <groupId>org.apache.maven.plugins</groupId>
	            <artifactId>maven-compiler-plugin</artifactId>
	            <version>3.1</version>
	            <configuration>
	                <source>1.8</source>
	                <target>1.8</target>
	            </configuration>
	        </plugin>
	    </plugins>
	</build>
	```

- or properties
	```
	<properties>
	    <maven.compiler.source>1.8</maven.compiler.source>
	    <maven.compiler.target>1.8</maven.compiler.target>
	</properties>
	```

# Note
- 调整完「Project Structure」和「Setting」后，重新调用「Project Structure」会发布「Settings」的配置被 **重置为 1.5 版本**


# Reference
- [IntelliJ IDEA里Maven默认情况下编译版本为JDK1.5](https://blog.csdn.net/gnail_oug/article/details/77507614)
