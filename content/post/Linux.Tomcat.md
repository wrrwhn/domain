---
title: "Linux.Tomcat"
date: "2017-08-16"
categories:
 - "整理"
tags:
 - "Linux"
 - "Tomcat"
toc: true
---


## 参考
- [tomcat中server.xml配置详解](http://blog.csdn.net/zcyhappy1314/article/details/10356909)
- [Tomcat server.xml配置示例](http://www.importnew.com/17124.html)
- [service.xml](http://doc.yqjdcyy.com/test.server.xml)


## 软件
- java
- tomcat
    - `scp -rv /data/service/tomcat root@39.108.103.85:/data/service/tomcat`

## 目录结构
- data
    - service
        - bin
            - 运行脚本
                - [wechat-forum-dev-start.sh](http://doc.yqjdcyy.com/wechat-forum-dev-start.sh)
                - [wechat-forum-dev-stop.sh](http://doc.yqjdcyy.com/wechat-forum-dev-stop.sh)
                - [deploy-instance.sh](http://doc.yqjdcyy.com/deploy-instance.sh)
        - logs
            - wechat-forum-dev
                - catalina.2017-08-15.log
                - catalina.2017-08-15.out
                - host-manager.2017-08-15.log
                - localhost_access_log.2017-08-15.txt
                - manager.2017-08-15.log
            - wechat-forum-dev_gc.log
        - run
            - wechat-forum-dev.pid
                - 9015
        - tomcat
            - bin
                - 启动、运行的相关二进制启动脚本
                - startup.sh
                - shutup.sh
                - version.sh
                    - CATALINA_BASE
                        - 实例运行目录
                    - CATALINA_HOME
                        - 安装目录
                    - CATALINA_TMPDIR
                    - JRE_HOME
                    - CLASSPATH
            - conf
                - catalina.policy
                    - 安全策略
                - catalina.properties
                - logging.properties
                - server.xml
                    - 主配置文件
                - context.xml
                    - 特殊配置全局选项
                - tomcat-users.xml
                    - 授权访问用户配置
                - tomcat-users.xsd
                    - 授权用户名、密码格式限制
                - web.xml
                    - web 应用全局配置描述
            - lib
                - 启动的所需 Jar-file
            - logs
            - webapps
            - work
            - temp
        - webapps
            - wechat-forum-dev
                - conf
                - logs
                - temp
                - webapps
                - work

## 备注
### 辅助脚本
- `scp -r /data/service/bin/deploy-instance.sh* root@39.108.103.85:/data/service/bin`
- `scp -r /data/service/webapps/basic-portal/conf root@39.108.103.85:/data/service/webapps/wechat-forum-dev`
