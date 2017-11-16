+++
date = "2017-02-23T22:07:46+08:00"
title = "常用CMD指令"
draft = false
tags = ["整理","CMD"]
share = true
+++

[TOC]

### 强制关闭占用指定端口服务
- 查询指定窗口是否被占用： netstat -aon|findstr "9050"
- 查询哪个应用占用了指定端口： tasklist|findstr "2748"
- 关闭相应的进程：taskkill /f /t /im adb.exe
- 常用：taskkill /f /t /im java.exe


### 硬盘检测及修复
- 检测指定盘符： chkdsk C:
- 若有异常时进行修复： chkdsk c: /f
- 注：若正在使用，可选择【Y】，待下次启动时检测


### 强制登录
- 发现超出并发人数的情况下，输入如下指令：mstsc /v:192.168.16.99:3389 /admin。
+ 其中192.168.16.99:3389为连接的网址和端口，admin表示管理员登陆。
 注：此例在win7远程win2003校验通过。


### 转换成系统隐藏文件
- 转换
- attrib +s +h [fileName|folderaName]
- 进入
- cd **/[fileName|folderaName]
- 恢复
- attrib -s -h [fileName|folderaName]


### 临时更改环境变量
- set GOPATH=%GOPATH%;xxxxxx


### 显示目录下文件
- `tree /f`

### 查看 Linux 系统版本
- `lsb_release -a`
