---
title: "Go.安装配置"
date: "2017-06-28"
categories:
 - "整理"
tags:
 - "Go"
toc: true
---


## 配置
- GOROOT
    - 配置 GO 的可执行文件路径
    - D:\server\go\1.8
- PATH
    - 将 Go 相关可执行文件列表入搜索路径中
    - %GOROOT%\bin
- GOPATH
    - 目录列表，类似于maven中的repository目录，引用库
    - 其中第一个地址将作为 go get 的下载目录
    - D:\server\go\lib
    - D:\work\git\yao\go\Hello_Go
