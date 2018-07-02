---
title: "Linux.Scp"
date: "2017-11-15"
categories:
 - "整理"
tags:
 - "Linux"
toc: true
---


## 参考
- http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/scp.html

## 特点
- scp 为加密传输
- scp 消耗资源少，不会提高多少系统负荷
    - rsync 快一点，但`小`文件多时会导致`硬盘 I/O 非常高`

## 格式
- `scp [参数] [原路径] [目标路径]`

## 参数
- `-1`
    - 强制scp命令使用协议ssh1
- `-2`
    - 强制scp命令使用协议ssh2
- `-4`
    - 强制scp命令只使用IPv4寻址
- `-6`
    - 强制scp命令只使用IPv6寻址
- `-B`
    - 使用批处理模式（传输过程中不询问传输口令或短语）
- `-C`
    - 允许压缩。（将-C标志传递给ssh，从而打开压缩功能）
- `-p`
    - 留原文件的修改时间，访问时间和访问权限。
- `-q`
    - 不显示传输进度条。
- `-r`
    - **递归复制**整个目录。
- `-v`
    - 详细方式显示输出。
    - scp和ssh(1)会显示出整个过程的调试信息。这些信息用于调试连接，验证和配置问题。
- `-c`
    - cipher 以cipher将数据传输进行加密，这个选项将直接传递给ssh。
- `-F`
    - ssh_config 指定一个替代的ssh配置文件，此参数直接传递给ssh。
- `-i`
    - identity_file 从指定文件中读取传输时使用的**密钥文件**，此参数直接传递给ssh。
- `-l`
    - limit 限定用户所能使用的**带宽**，以Kbit/s为单位。
- `-o`
    - ssh_option 如果习惯于使用ssh_config(5)中的参数传递方式，
- `-P`
    - port 注意是大写的P, port是指定数据传输用到的**端口号**
- `-S`
    - program 指定加密传输时所使用的程序。此程序必须能够理解ssh(1)的选项。

## 事例
- `scp -r root@10.6.159.147:/opt/soft/test /opt/soft/`
- `scp /data/jenkins/jobs/basic-portal-jianguo/workspace/code/basic-portal/target/basic-portal.war root@106.14.223.58:/data/service/webapps/`
- `scp -rv /data/service/tomcat root@39.108.103.85:/data/service`
