---
title: "Java.Jar"
date: "2018-04-26"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---


# Package
- Create Artifacts
	- Open 「Project Structure」
		- ![1-1.png](http://otzm88f21.bkt.clouddn.com/121dec5a-0f64-476f-99c4-7e9417404346.png)
	- Create Artifacts
		- ![1-2.png](http://otzm88f21.bkt.clouddn.com/e1748e73-bf6a-44d2-b778-15bd2d43d5aa.png)
	- Setting
		- ![1-3.png](http://otzm88f21.bkt.clouddn.com/a85b2edf-ad9e-482b-9d95-87387f4ed4d8.png)

- Build Artifacts
	- ![2-1.png](http://otzm88f21.bkt.clouddn.com/31196862-a83a-4660-893d-6dbe7c1ddc31.png)
	- ![2-2.png](http://otzm88f21.bkt.clouddn.com/a1d8d5e6-e031-4878-91b8-8334ff11f759.png)

# Run
- `java -jar PDFBox.jar C:\Users\Yao\Desktop\anythingToPPTX\from\sliders.pdf`
	- `PDFBox.jar中没有主清单属性`

# Fix
- Open MANIFEST.MF
	- PDFBox.jar/META-INF/MANIFEST.MF
- Add **Main-Class**
	- `Main-Class: com.yao.main.PDFBoxMain`
- Save


# Reference
- 主要
	- [How to Create an Executable JAR with Maven](http://www.baeldung.com/executable-jar-with-maven)
	- [idea打包java可执行jar包](http://www.cnblogs.com/blog5277/p/5920560.html)
- 补充
	- [A如何打包可运行jar的一个问题](http://bglmmz.iteye.com/blog/2058785)
	- [JAR包中的MANIFEST.MF文件详解以及编写规范](https://www.cnblogs.com/EasonJim/p/6485677.html)
	- [Convert Java to EXE — Why, When, When Not and How](https://www.excelsior-usa.com/articles/java-to-exe.html)
