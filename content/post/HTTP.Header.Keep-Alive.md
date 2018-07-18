---
title: "HTTP.Header.Keep-Alive"
date: "2018-07-18"
categories:
 - "整理"
tags:
 - "HTTP"
toc: true
---


# Introduce
- 参见 [HTTP.Header.Connection](/post/http.header.connection)

# Parameters
## timeout
- 指明空闲链接的 **最短保持**链接 **时间**
- 单位为秒
- 当 TCP 未设置保持连接时，超时时长比 TCP 超时时长更长情况下则忽略

## max
- 指明关闭前 **最多**可请求的 **次数**

# Example

```
HTTP/1.1 200 OK
Connection: Keep-Alive
Content-Encoding: gzip
Content-Type: text/html; charset=utf-8
Date: Thu, 11 Aug 2016 15:23:13 GMT
**Keep-Alive: timeout=5, max=1000**
Last-Modified: Mon, 25 Jul 2016 04:32:39 GMT
Server: Apache

(body)
```


# Reference
## 官方
- [Keep-Alive](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Keep-Alive)