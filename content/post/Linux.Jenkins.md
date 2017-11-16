+++
date = "2017-08-17T22:07:46+08:00"
title = "Linux.Jenkins"
draft = false
tags = ["整理","Linux"]
share = true
+++

[TOC]

## 参考
- [Centos yum install jenkins](https://segmentfault.com/a/1190000006751968)
- [jenkins实战之jenkins安装部署](http://blief.blog.51cto.com/6170059/1846017)
- [Tomcat 部署方式](https://wiki.jenkins-ci.org/display/JENKINS/Tomcat)
- [jenkins.io](https://jenkins.io/index.html)
- [jenkins.war](wget http://mirrors.jenkins-ci.org/war/latest/jenkins.war)
- [jenkins集成git，构建项目报错，做了ssh的认证，还是提示 Host key verification failed](https://www.oschina.net/question/2819114_2217616)
- [[WARNING] Failed to create parent directories for tracking file](https://my.oschina.net/u/2503743/blog/755230)

## 流程
### war
- DOWNLOAD
    - `wget http://mirrors.jenkins-ci.org/war/latest/jenkins.war`
- COPY
    - `cp jenkins.war /data/service/webapps/jenkins/webapps/ROOT.war`
- CONFIG.JENKINS.TOMCAT
    ```
    CATALINA_OPTS="-server -DJENKINS_HOME=/root/.jenkins -Xms528m -Xmx528m -XX:PermSize=256m -XX:MaxPermSize=358m"
    export CATALINA_OPTS
    ```

### yum
- YUM.CONFIG
    - `wget -O /etc/yum.repos.d/jenkins.repo http://jenkins-ci.org/redhat/jenkins.repo`
    - `rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key`
- YUM
    - `yum install jenkins`
- JENKINS.CONFIG
    - `/etc/sysconfig/jenkins`
        ```
        JENKINS_PORT="8016"
        ```
- START
    - `service jenkins start`
- FIREWALL
    - firewalld
        ```
        firewall-cmd --permanent --zone=public --add-port=8016/tcp
        firewall-cmd --reload
        ```
    - aliyun
        - 将 8016 添加至安全策略里
- INIT
    - login
        - http://39.108.103.85:8016/
    - input init password
        - `less /var/lib/jenkins/secrets/initialAdminPassword`
    - Install suggested plugins
    - create first admin user
    - start using jenkins
- *.CONFIG
    - `http://39.108.103.85:8016/configureTools/`
        - JAVA
        - MAVEN
        - GIT
    - `http://39.108.103.85:8016/pluginManager/available`
        - 安装「Maven Integration plugin」插件
- CREATE
    ```
    [General]
    Project.name=dev-0-umay-core

    [Code]
    type= git
    Reposiotry.Url= https://gitee.com/Jeval/umay.git
    Credentials= User[name/ password]
    Branch= */dev

    [Build]
    Root.POM= umay-core/pom.xml
    Goals&Options
    ```

### user
- JENKINS_USER
    ```
    vim /etc/sysconfig/jenkins
         JENKINS_USER="jenkins"    -> JENKINS_USER="appuser"

    chown -R appuser:appuser /var/lib/jenkins /var/log/jenkins
    ```
- ReStart
    - `service jenkins restart`

## 异常
### Could not read from remote repository.
- 尝试 SSH 方式无果
    - ~/.ssh + `ssh-keygen`
    - /var/lib/jenkins/.ssh + `ssh-keygen`
- 转为使用 HTTP + 账号密码

### Failed to create parent directories for tracking file
- 设置 repo 至有权限目录
    - `-Dmaven.repo.local=/var/lib/jenkins/.m2/repo`

