---
title: "Hello.Maven.Wagon"
date: "2020-04-17"
categories:
 - "整理"
tags:
 - "Maven"
 - "Wagon"
toc: true
---


# 简介
- 供仓库之间的资源查看和传递

# 常用功能
## `wagon:upload-single`
- 描述
    - 将本地文件上传至指定位置（本地或线上服务）
- 示例
    ```xml
    <build>
        <extensions>
            <extension>
                <groupId>org.apache.maven.wagon</groupId>
                <artifactId>wagon-ssh</artifactId>
                <version>2.3</version>
            </extension>
        </extensions>
        <plugins>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>wagon-maven-plugin</artifactId>
                <version>2.0.0</version>
                <configuration>
                    <fromFile>target/commons-1.0-SNAPSHOT.jar</fromFile>
                    <!-- Windows -->
                    <url>file://d:/download</url>
                    <!-- Linux -->
                    <!-- <url>scp://{user}:{password}@{ip}/${path}</url>-->
                </configuration>
            </plugin>
        </plugins>
    </build>
    ```

## `wagon:sshexec`
- 描述
    - 在指定服务器上执行一系列指令
- 示例
    ```xml
    <build>
        <extensions>
            <extension>
                <groupId>org.apache.maven.wagon</groupId>
                <artifactId>wagon-ssh</artifactId>
                <version>2.8</version>
            </extension>
        </extensions>
        <plugins>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>wagon-maven-plugin</artifactId>
                <version>2.0.0</version>
                <configuration>
                    <fromFile>target/commons-1.0-SNAPSHOT.jar</fromFile>
                    <url>file://d:/download</url>
                    <commands>
                        <!-- Windows 指令暂时无法成功 -->
                        <!-- <command>cmd.exe /k echo hello</command>-->
                        <!-- Linux 环境下指令 -->
                        <command>nohup java -jar /home/xxg/Desktop/test.jar > /home/xxg/Desktop/nohup.out 2>&1 &</command>
                    </commands>
                    <displayCommandOutputs>true</displayCommandOutputs>
                </configuration>
            </plugin>
        </plugins>
    </build>
    ```

# 参考
- [Wagon Maven Plugin](http://www.mojohaus.org/wagon-maven-plugin/)
- [In Maven how do I copy files using the wagon plugin?](https://stackoverflow.com/questions/6291221/in-maven-how-do-i-copy-files-using-the-wagon-plugin)
- [wagon-maven-plugin](https://ileler.gitbooks.io/note/content/blog/other/maven/wagon-maven-plugin.html)
    - 翻译版本，有详细的事例