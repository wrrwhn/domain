---
title: "Windows.Service"
date: "2017-07-20"
categories:
 - "整理"
tags:
 - "Windows"
 - "Service"
toc: true
---


# Windows 程序服务化
## 参考
- [SC](https://technet.microsoft.com/en-us/library/bb490995.aspx)
- [NSSM](https://nssm.cc/)
- [windows service startup timeout](https://serverfault.com/questions/622432/how-do-i-increase-windows-service-startup-timeout)
- [reset service timeout](https://www.codetwo.com/kb/how-to-extend-the-timeout-for-services-if-they-do-fail-to-start/)
- [服务安装启动报错误1053](http://www.jianshu.com/p/bbd414fb71c6)

## SC - Service Controller
### 语法
- `sc [ServerName] [command] [Optionname= Optionvalues]`

### 常用参数
- config
    - 更新服务配置
    - `sc config nsqd start= auto`
- control
    - 发送控制指令
    - `sc [<ServerName>] control [<ServiceName>] [{paramchange | netbindadd | netbindremove | netbindenable | netbinddisable | <UserDefinedControlB>}]`
- create | delete
    - 创建|删除服务
    - `sc create nsqd binPath= .\nsqd.exe start= auto`
- start | stop
    - 启动|停止服务
    - `sc start nsqd`

### 示例
```
@echo off

sc create nsqd binPath= .\nsqd.exe start= auto
sc start nsqd

echo DONE!
pause
```

### 注意事项
- 等号应与后面值间隔以空格
- 删除服务前，应先停止服务
- 可键入`sc -help` 或具体指令 `sc start` 查看帮助文档

### 异常
- 异常排查
    - 查看系统日志，来源为 「Service Control Manager」
- 1053:服务没有及时响应启动或控制请求
    - C# 服务，可能因为 .Net Framework 版本不一致
    - 可查度参考文献中的方法，进行注册表中的「ServicesPipeTimeout 」值的修复

## NSSM - the Non-Sucking Service Manager
### 常用语法
- `nssm install <server-name> D:\server\go\nsq\0.3.8\bin\nsqd.exe`
- `nssm remove <server-name>`

### 缺点
- **部分软件无法正常运行！**
