---
title: "Tomcat.AccessLog"
date: "2017-10-21"
categories:
 - "整理"
tags:
 - "Tomcat"
toc: true
---


# Tomcat. AccessLog

## 参考
- [Tomcat Access Log配置](http://xiaobaoqiu.github.io/blog/2014/12/30/tomcat-access-logpei-zhi/)
- [Class AccessLogValve](https://tomcat.apache.org/tomcat-7.0-doc/api/org/apache/catalina/valves/AccessLogValve.html)

## 作用
- 设置请求日志中的日志输出格式

## 详解
### 位置
- conf.server.xml
    - value.className= *.AccessLogValve

### 参数
- `className`
    - 执行类
        - `org.apache.catalina.valves.AccessLogValve`
            - 默认
        - `org.apache.catalina.valves.FastCommonAccessLogValve`
            - 仅支持 `common` 和 `combined patterns`           
- `directory`
    - 产生文件所存放目录的[绝对|相对]路径
        - 相对于 `$CATALINA_HOME`
        - 默认为 `logs`

- `pattern`
    - `%a - 远程IP地址`
    - `%A - 本地IP地址`
    - `%b - 发送的字节数(Bytes sent), 不包括HTTP headers的字节，如果为0则展示'-'`
    - `%B - 发送的字节数(Bytes sent), 不包括HTTP headers的字节`
    - `%h - 远程主机名称(如果resolveHosts为false则展示IP)`
    - `%H - 请求协议`
    - `%l - 远程用户名，始终为'-'(Remote logical username from identd)`
    - `%m - 请求的方法(GET, POST等)`
    - `%p - 接受请求的本地端口`
    - `%q - 查询字符串，如果存在，有一个前置的'?'`
    - `%r - 请求的第一行(包括请求方法和请求的URI)`
    - `%s - response的HTTP状态码(200,404等)`
    - `%S - 用户的session ID`
    - `%t - 日期和时间，Common Log Format格式`
    - `%u - 被认证的远程用户, 不存在则展示'-'`
    - `%U - 请求URL路径`
    - `%v - 本地服务名`
    - `%D - 处理请求的时间，单位为毫秒`
    - `%T - 处理请求的时间，单位为秒`
    - `%I - 当前请求的线程名(can compare later with stacktraces)`

- `prefix`
    - 指定每个 AccessLog 的文件名前缀
    - 默认为「access_log」

- `resolveHosts`
    - 是否通过 DNS lkkiup 将远程主机 IP 转换为对应主机
    - 默认为 false

- `suffix`
    - 指定每个 AccessLog 的文件名后缀
    - 默认为空

- `rotatable`
    - 决定是否需要切换日志文件
    - 默认为 true
    - 为 false 时，即所有文件打到同一个日志文件中，并且 fileDateFormat 参数也会被忽略

- `fileDateFormat`
    - 用于指定文件名中的日期格式，也决定了切换日志文件的策略
    - 如按小时生成日志文件，可设置为 `yyyy-MM-dd.HH`

- `condition`
    - 设置是否打开条件日志
    - 如设置 `condition=skip`，当 `ServletRequest.getAttribute("skip")== null` 时，该请求会被记录
    - 可配合 filter 设置

### Cookie| Header| Session| ServletRequest
- `%{xxx}i 请求headers的信息`
- `%{xxx}o 响应headers的信息`
- `%{xxx}c 请求cookie的信息`
- `%{xxx}r xxx是ServletRequest的一个属性`
- `%{xxx}s xxx是HttpSession的一个属性`


## 示例
- 默认
    - `<Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs" prefix="access." suffix=".log" pattern="%h %l %u %t "%r" %s %b "%{Referer}i" "%{User-Agent}i" %{X-Forwarded-For}i "%Dms"" resolveHosts="false"/>`
- 服务器配置
    - `<Valve className="org.apache.catalina.valves.AccessLogValve" directory="/data/service/logs/${tomcat.instance.name}-test" prefix="localhost_access_log" suffix=".txt" pattern="%h %{x-forwarded-for}i %l %u %t &quot;%r&quot; %s %b %D" />`
