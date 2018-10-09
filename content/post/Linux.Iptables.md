---
title: "Linux.Iptables"
date: "2018-10-09"
categories:
 - "整理"
tags:
 - "Linux"
toc: true
---


# 介绍
## 作用
- 配置 `Linux` 内核防火墙的命令行工具
    - 检测、修改、转发、重定向和丢弃 `IPv4` 数据包
    - 针对 `IPv6` 需使用 `ip6tables`

## 组成
- Tables
    - 作为具有相同功能规则的合集

        | table    | work                                          |
        |----------|-----------------------------------------------|
        | raw      | 网址过滤，raw 中的数据包不会被系统跟踪         |
        | filter   | 包过滤，存放所有与防火墙相关操作的默认表       |
        | nat      | 用于**网络地址转换**<br>例如端口转发          |
        | mangle   | 用于对特定数据包的修改<br>例如损坏数据包      |
        | security | 用于**强制访问控制** 网络规则<br>例如 SELinux |

- Chains
    - 为按顺序排列的规则列表
    - 是表的组成元件
        - `filter= INPUT+ OUTPUT+ FORWARD`
        - `nat= PREROUTING+ OUTPUT`
        - `mangle= PREROUTING+ INPUT+ FORWARD+ OUTPUT+ POSTROUTING`
    - 默认无规则，而添加的默认规则为 `ACCEPT`
    - 而新添加的规则，默认置于队尾，需在前面规则通过的基础上才能生效
    - 链路功能

        | 链路类型   | 处理数据类型                   |
        |------------|--------------------------|
        | INPUT      | 数据包**目标地**是**本地**主机 |
        | OUTPUT     | 处理输出数据包                 |
        | FORWARD    | 数据包**目标地**是**其它**主机 |
        | PREROUTING | 用于目标地址转换（DNAT）         |
        | POSTOUTING | 用于源地址转换（SNAT）           |

- Rules
    - 规则用于过滤数据包
    - 由一个目标+ 多个匹配组成
        - 顺序
            - **在通过多个匹配后，执行目标**
        - 目标
            - 使用 `-j|-jump` 指定
            - 可指定自定义链、内置的特定目标、不终止
                - 内置目标
                    - `ACCEPT`
                    - `DROP`
                    - `QUEUE`
                    - `RETURN`
                - 目标扩展
                    - `REJECT`
                    - `LOG`
        - 匹配
            - 端口
                - `eth0`
                - `eth1`
            - 类型
                - `ICMP`
                - `TCP`
                - `UDP`
            - 目标端口
                - 指定端口，如 `8080`
    - 动作

        |    动作    |         实现功能        |
        |------------|-------------------------|
        | ACCEPT     | 接收数据包              |
        | DROP       | 丢弃数据包              |
        | REDIRECT   | 重定向、映射、透明代理  |
        | SNAT       | 源地址转换              |
        | DNAT       | 目标地址转换            |
        | MASQUERADE | IP伪装（NAT），用于ADSL |
        | LOG        | 日志记录                |        

- Traversing Chains
    - 描述接收到的网络数据包，按照怎样的顺序通过表的交通管制链

- Modules
    - 通过模块以扩展功能，实现更为复杂的过滤
    - 模块
        - `connlimit`
            - 限制同一 IP 连接数
        - `conntrack`
            - 追踪、记录一个连接的状态
        - `limit`
            - 限速
        - `recent`
            - 大型地址列表
            - 配合限制登录等操作使用


## 路由

- 网络请求由 `<Network>` 作为起点流通
- 本地请求则由 `<local process>` 作为起点流通

```
                               XXXXXXXXXXXXXXXXXX
                             XXX     Network    XXX
                               XXXXXXXXXXXXXXXXXX
                                       +
                                       |
                                       v
 +-------------+              +------------------+
 |table: filter| <---+        | table: nat       |
 |chain: INPUT |     |        | chain: PREROUTING|
 +-----+-------+     |        +--------+---------+
       |             |                 |
       v             |                 v
 [local process]     |           ****************          +--------------+
       |             +---------+ Routing decision +------> |table: filter |
       v                         ****************          |chain: FORWARD|
****************                                           +------+-------+
Routing decision                                                  |
****************                                                  |
       |                                                          |
       v                        ****************                  |
+-------------+       +------>  Routing decision  <---------------+
|table: nat   |       |         ****************
|chain: OUTPUT|       |               +
+-----+-------+       |               |
      |               |               v
      v               |      +-------------------+
+--------------+      |      | table: nat        |
|table: filter | +----+      | chain: POSTROUTING|
|chain: OUTPUT |             +--------+----------+
+--------------+                      |
                                      v
                               XXXXXXXXXXXXXXXXXX
                             XXX    Network     XXX
                               XXXXXXXXXXXXXXXXXX
```

# 指令

## 格式
- `iptables <option> (<args>)`
- `iptables -t 表名 <-A/I/D/R> 规则链名 [规则号] <-i/o 网卡名> -p 协议名 <-s 源IP/源子网> --sport 源端口 <-d 目标IP/目标子网> --dport 目标端口 -j 动作`

## 详解

|      选项     |                     作用                     |
|---------------|----------------------------------------------|
| -t <表>       | 指定要操纵的表                               |
| -A            | 向规则链中**添加**条目                       |
| -D            | 从规则链中删除条目                           |
| -i            | 向规则链中插入条目                           |
| -R            | **替换**规则链中的条目                       |
| -L            | **显示**规则链中已有的条目                   |
| -F            | **清除**规则链中已有的条目                   |
| -Z            | 清空规则链中的数据包计算器和字节计数器       |
| -N            | 创建新的用户自定义规则链                     |
| -P            | 定义规则链中的**默认目标**                   |
| -h            | 显示**帮助**信息                             |
| -p            | 指定要匹配的数据包**协议类型**               |
| -s            | 指定要匹配的数据包**源IP地址**               |
| -j <目标>     | 指定要**跳转的目标**                         |
| -i <网络接口> | 指定数据包**进入**本机的网络**接口**         |
| -o <网络接口> | 指定数据包要**离开**本机所使用的网络**接口** |


# 示例
## 限制每IP在一分钟内最多对服务器只能有3个SSH连接

```sh
iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --update --seconds 60 --hitcount 3 --name SSH -j LOG --log-prefix "SSH Attack"
iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --update --seconds 60 --hitcount 3 --name SSH -j DROP
iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --set --name SSH -j ACCEPT
```

## 查看并删除第8条规则

```sh
iptables -L -n --line-numbers
iptables -D INPUT 8
```

## 添加规则

```sh
# 配置规则
    # 允许 192.168.1.0/24 针对 22 端口的 tcp 请求
    iptables -A INPUT -s 192.168.1.0/24 -p tcp --dport 22 -j ACCEPT
    # 开放外网对 80 端口的 tcp 请求
    iptables -A INPUT -p tcp --dport 80 -j ACCEPT
    # 限制来自 123.45.6.7 的请求
    iptables -I INPUT -s 123.45.6.7 -j DROP

# 重启
service iptables restart

# 状态
service iptables status
```

## 配置查看

```sh
cat /etc/sysconfig/iptables
```



# 参考

## IPTables
- [Linux - CentOS6下操作防火墙(iptables)](https://blog.csdn.net/J080624/article/details/78859646)
- [iptables详解（1）：iptables概念](http://www.zsythink.net/archives/1199)
- [Iptables (简体中文)](https://wiki.archlinux.org/index.php/Iptables_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

## 安全
- [Iptables防攻击模块介绍（五）](http://www.ywnds.com/?p=1906)
- [通过iptables配置加强服务器安全](https://delcoding.github.io/2017/12/iptables/)

## 扩展
- [网络地址转换](https://en.wikipedia.org/wiki/Network_address_translation)
- [损坏数据包](https://en.wikipedia.org/wiki/Mangled_packet)
- [强制访问控制](https://wiki.archlinux.org/index.php/Security#Mandatory_access_control)
- [SELinux ](http://lwn.net/Articles/267140/)