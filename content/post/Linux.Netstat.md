---
title: "Linux.Netstat"
date: "2018-11-04"
categories:
 - "整理"
tags:
 - "Linux"
toc: true
---


# Netstat
## 作用
- 可于内核中访问网络连接状态及其相关信息
- 用于显示**网络**连接、**路由**表和每个网络**接口**设备的状态信息

## 格式
- `netstat [-a][-e][-n][-o][-p Protocol][-r][-s][Interval]`

## 参数
| 参数                   | 作用                                                       |
|------------------------|----------------------------------------------------------|
| `-a/-all`              | 显示所有socket，不论是否正在监听                            |
| `-c/-continuous`       | 持续每秒进行选中信息的输出显示                             |
| `-i/-interfaces=iface` | 显示所有的网络接口                                         |
| `-n/-numeric`          | 以数值地址形式进行展示                                     |
| `-r/-route`            | 显示内核路由表，同 `route -e`                               |
| `-t/-tcp`              | 显示 TCP 协议的连接情况                                    |
| `-u/-udp`              | 显示 UDP 协议的连接情况                                    |
| `-v/-verbose`          | 详细显示正在进行的工作                                     |
| `-p/-program`          | 显示套接字关联的程序名称和 PID                             |
| `-e/-extend`           | 显示额外信息，如 uid 等<br>若连续两次调用则显示最大程度明细 |
| `-o/-timers`           | 显示与网络计时器相关的信息                                 |
| `-s/-statistics`       | 展示每个协议的摘要统计                                     |
| `-l/-listening`        | 仅显示监听中的套接字                                       |


## 序列图
- ![TCP.jpg](http://otzm88f21.bkt.clouddn.com/bb53031b-ec76-4c75-bae5-6f373f59cccb.jpg)
- ![TCP-2.jpg](http://otzm88f21.bkt.clouddn.com/bc17ba30-587b-4f0b-a409-80be30673389.jpg)


## 输出
### State

| State         | Desc                                                                                       |
|---------------|--------------------------------------------------------------------------------------------|
| LISTENING     | 正在监听端口                                                                               |
| SYNC_SEND     | 已发送 `SYN` 报文等待连接请求建立                                                          |
| SYNC_RECEIVED | 已接收 `SYN` 报文的连接请求后，发送 `SYN` 回包后的                                          |
| ESTABLISHED   | 连接已建立，开始进行数据传输                                                                |
| CLOSE_WAIT    | 服务端等待关闭<br>回应客户端的 `FIN`报文以 `ACK`报文，无后缀则关闭 Socket 并发送 `FIN` 报文 |
| FIN_WAIT_1    | 客户端请求断开，发送 `FIN` 报文后的等待状态<br>由于服务端响应及时，观察频率低                |
| FIN_WAIT_2    | 客户端等待数据传输完成后，服务端发送`FIN` 报文确认的过程<br> 半连接                         |
| LAST_ACK      | 被动关闭方等待最终 `ACK` 报文的确认                                                        |
| TIME_WAIT     | 接收 `FIN` 并返回 `ACK` 报文后，等待 `2MSL` 后返回 CLOSED 状态                              |
| CLOSING       | 等待远程 TCP 对连接的确认                                                                  |
| CLOSED        | 无连接状态链路                                                                             |


## 示例
### 过滤 TCP 连接统计
- `netstat -nat |awk '{print $6}'| sort | uniq -c`

### 查询指定端口的 TCP 连接数
- `netstat -ant | grep -i "80" | wc -l`

### 已连接链路数
- `netstat -an | grep ESTABLISHED | wc -l`


# 参考
- [netstat用法及TCP state解析](https://www.cnblogs.com/vigarbuaa/archive/2012/03/07/2383064.html)
- [查看linux中的TCP连接数](https://blog.csdn.net/he_jian1/article/details/40787269)