---
title: "Linux.Firewalld"
date: "2017-08-14"
categories:
 - "整理"
tags:
 - "Linux"
toc: true
---


# firewalld

## 参考
- [CentOS 7 为firewalld添加开放端口及相关资料](http://www.cnblogs.com/hubing/p/6058932.html)
- [使用FirewallD替代Iptables的一些配置](http://jim0.com/server/firewalldconfig.html)

## 指令
### 常见
- 添加端口
    - `firewall-cmd --permanent --zone=public --add-port=3306/tcp`
- 重启
    - `firewall-cmd --reload`
- 添加服务
    - `firewall-cmd --permanent --add-service=mysql`

### 所有
- 通用
    - `firewall-cmd [--zone=<zone>] --add-port=<port>[-<port>]/<protocol> [--timeout=<seconds>]`

- 运行
    - 查看运行状态
        - `systemctl status firewalld.service`
    - 开关
        - `systemctl [stop|start] firewalld`
        - `firewall-cmd --reload`
    - 服务相关
        - `firewall-cmd --list-services`
        - `firewall-cmd --[add|remove]-service=mysql`
        - `firewall-cmd --permanent --[add|remove]-service=mysql`
    - 端口相关
        - `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
        - `firewall-cmd --zone=public --list-ports`
        - `firewall-cmd --zone=public --query-port=3306/tcp`

- 开机启动
    - 设置开机启动
        - `systemctl enable firewalld.service`
    - 检查是否开机启动
        - `systemctl is-enabled firewalld.service`

- 相关配置
    - `/etc/firewalld/firewalld.conf`
        - `DefaultZone`
            - `drop`
            - `block`
            - `public`
            - `external`
            - `dmz`
            - `work`
            - `home`
            - `internal`
            - `trusted`
    - `/etc/firewalld/zones/public.xml`
