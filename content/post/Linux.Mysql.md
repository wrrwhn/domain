+++
date = "2017-08-14T22:07:46+08:00"
title = "Linux.Mysql"
draft = false
tags = ["整理","Linux" ,"Mysql" ]
share = true
+++


[TOC]

## 参考
- [centos7 mysql数据库安装和配置](https://www.cnblogs.com/starof/p/4680083.html)
- [CentOS 7 安装 MySQL](https://yq.aliyun.com/articles/47237)
- [Linux下安装MySQL](http://www.jianshu.com/p/f4a98a905011)
- [centos7 mysql数据库安装和配置](http://www.voidcn.com/article/p-aqkchgpx-xv.html)

## 环境
- Linux
    ```
    LSB Version:    :core-4.1-amd64:core-4.1-noarch
    Distributor ID: CentOS
    Description:    CentOS Linux release 7.3.1611 (Core)
    Release:        7.3.1611
    Codename:       Core
    ```

## 安装
### 默认
- `yum install mysql`
- `yum install mysql-server`
- `yum install mysql-devel`
###
- `wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm`
- `rpm -ivh mysql-community-release-el7-5.noarch.rpm`
- `yum install mysql-community-server`
- `mysql_upgrade`

## 初始化
### Mysql 初始化
- `mysql_secure_installation`

### 直接设置密码
- `set password for 'root'@'localhost' =password('root.password');`

## 启动
### 服务启动
- systemctl
    - `systemctl start mysqld`
    - `systemctl status mysqld`
    - `systemctl stop mysqld`
- service
    - `service mysqld start`
    - `service mysqld stop`
    - `service mysqld status`
    - `service mysqld restart`

### 开机启动
- `systemctl enabled mysql`
- `systemctl is-enabled mysql`

### 防火墙
- firewalld
    - `firewall-cmd --permanent --zone=public --add-port=3306/tcp`
    - `firewall-cmd --permanent --zone=public --add-port=3306/udp `
    - `firewall-cmd --reload`

## 环境准备
### 创建数据库
- `mysql -uroot -p`
    - `root.password`
- `create database wechat_forum;`
- `create database wechat_forum_dev;`

### 创建账号& 分配权限
- `grant all privileges on *.* to root@'%'identified by 'root.password';`

- `CREATE USER 'appuser'@'%' IDENTIFIED BY 'appuser.password';`
- `GRANT ALL ON wechat_forum_dev.* TO 'appuser'@'%';`
- `GRANT ALL ON wechat_forum.* TO 'appuser'@'%';`

- `flush privileges;`

- `mysql -uappuser -p`
    - `appuser.password`

## 异常排查
### set password 失败
- 提示
    - `ERROR 1558 (HY000): Column count of mysql.user is wrong. Expected 43, found 42. Created with MySQL 50552, now running 50637. Please use mysql_upgrade to fix this error.`
- 解决
    - `mysql_upgrade`

### 3306 无法对外开放
- `which mysqld`
    - `/usr/sbin/mysqld`
- `/usr/sbin/mysqld --verbose --help | grep -A 1 'Default options'`
    ```
    Default options are read from the following files in the given order:
    /etc/my.cnf /etc/mysql/my.cnf /usr/etc/my.cnf ~/.my.cnf
    ```
- `netstat -ntlp`
    ```
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name   
    tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      2129/sshd           
    tcp6       0      0 :::3306                 :::*                    LISTEN      9414/mysqld   
    ```
- *阿里云 ECS 需要对外开放端口！*


## 备注
### 查看 Linux 版本
- `lsb_release -a`

### 查看 Mysql 版本
- `mysql -V`
