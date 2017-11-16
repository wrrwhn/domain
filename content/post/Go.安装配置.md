+++
date = "2017-06-28T22:07:46+08:00"
title = "Go.安装配置"
draft = false
tags = ["整理","Go"]
share = true
+++

[TOC]

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
