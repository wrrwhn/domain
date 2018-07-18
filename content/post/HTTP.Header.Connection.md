---
title: "HTTP.Header.Connection"
date: "2018-07-18"
categories:
 - "整理"
tags:
 - "HTTP"
toc: true
---


# Introduce
## 作用
- 指定网络连接于请求完成后保持连接，允许同一服务器对后续请求的处理
- 使 **客户端**到 **服务器端**的连接持续有效，当出现对服务器的后继请求时，Keep-Alive功能 **避免了建立或者重新建立连接**
    - 多个请求 **共用同一 TCP 链接**
        - 减少重复请求时所需要的 TCP 握手
        - 异常上报也直接通过 TCP 上传
- ![File:HTTP_persistent_connection](https://upload.wikimedia.org/wikipedia/commons/d/d5/HTTP_persistent_connection.svg)


## 优点
- 减少 CPU 和内存的使用
- 减少 TCP 的链接和请求延迟

## 缺点
- CPU 转耗在众多的一次性连接上

## 建议
- 客户端与任务服务器之间不应维持超过 **2个** 链接

# Option
## close
- 指定客户端、服务端都将关闭链接
- HTTP/1.0 的默认选项

## keep-alive
- HTTP 1.1 的默认选项
- 除非请求头中指定 `Connection: Close`


# Supplement

## 判断数据是否传输完成
- Content-Length
    - 指定实体内容长度

- Transfer-Encoding: chunk

    ```
    Chunked-Body = *chunk 
                    "0" CRLF 
                    footer 
                    CRLF  
    chunk = chunk-size [ chunk-ext ] CRLF 
            chunk-data CRLF

    hex-no-zero = <HEX excluding "0">

    chunk-size = hex-no-zero *HEX 
    chunk-ext = *( ";" chunk-ext-name [ "=" chunk-ext-value ] ) 
    chunk-ext-name = token 
    chunk-ext-val = token | quoted-string 
    chunk-data = chunk-size(OCTET)

    footer = *entity-header
    ```

## 默认配置

| 服务             | 超时时长 |
|------------------|----------|
| Apache 2.0 httpd | 15s      |
| Apache 2.2 httpd | 5s       |



## HTTP.Keep-Alive 与 TCP.Keep-Alive 的区别
- 前者是为了让 TCP 戚时间更长，以便在同一 TCP 连接上进行多次请求
- 后者仅是为了检测 TCP 的连接状态
    - 通过向客户端发送空包，以判断连接状态

        | 配置项                                    | 数值 | 作用                                                              |
        |-------------------------------------------|------|-------------------------------------------------------------------|
        | `/proc/sys/net/ipv4/tcp_keepalive_time`   | 1800 | TCP 连接链接后，闲置指定时间后发送空包以检测连接状态<br>单位为`秒` |
        | `/proc/sys/net/ipv4/tcp_keepalive_intvl`  | 15   | 未收到请求方 ACK 请求情况下的间隔请求时间<br>单位为`秒`           |
        | `/proc/sys/net/ipv4/tcp_keepalive_probes` | 5    | 未收到请求方 ACK 请求情况下的尝试检测次数上限                     |


# Reference
## 官方
- [Connection](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Connection)
- [RFC 2616](https://tools.ietf.org/html/rfc2616)
- [Transfer-Encoding](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Transfer-Encoding)

## 补充
- [HTTP Keep-Alive是什么？如何工作？](http://www.nowamagic.net/academy/detail/23350305)
- [HTTP Keep-Alive模式](https://www.cnblogs.com/skynet/archive/2010/12/11/1903347.html)
- [http的keep-alive和tcp的keepalive区别](https://blog.csdn.net/oceanperfect/article/details/51064574)
- [KeepAlive，你优化了吗](http://51write.github.io/2014/04/09/keepalive/)