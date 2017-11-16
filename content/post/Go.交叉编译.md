+++
date = "2017-08-28T22:07:46+08:00"
title = "Go.交叉编译"
draft = false
tags = ["整理","Go"]
share = true
+++

[TOC]

# Go.交叉编译

## 参考
- [交叉编译](http://dmdgeeker.com/goBook/docs/ch01/cross_compile.html)
- [Golang 交叉编译](http://www.jianshu.com/p/4f79ae4f081c)
- [go tool cgo](http://wiki.jikexueyuan.com/project/go-command-tutorial/0.13.html)
- [Golang 在 Mac、Linux、Windows 下交叉编译](http://www.infocool.net/kb/Go/201705/355169.html)

## 作用
- Go作为编译型语言，在不同平台上需要编译生成不同格式的二进制包

## 参数
- `GOOS`
    - 程序构建目标环境的操作系统
- `GOARCH`
    - 程序构建目标环境的计算机架构
- `CGO_ENABLED`
    - 当值为 0 时表示设置 CGO 不可用

## 示例
### Linux 64bit
- `GOOS=linux GOARCH=amd64 go build -o app.linux`
    - `-o` 用于指定二进制文件名

### Linux 32bit
- `GOOS=linux GOARCH=386 go build`

### windows 64bit
- `GOOS=windows GOARCH=amd64 go build`

### windows 32bit
- `GOOS=windows GOARCH=386 go build`

### Mac OS 64bit
- `GOOS=darwin GOARCH=amd64 go build`

## 备注
- CGO
    - GO 语言自带工具，用于支持调用 C 语言代码的 GO 语言源码文件
- 1.5 以前版本需提交配置交叉编译环境
    - `CGO_ENABLED=0 GOOS=linux GOARCH=amd64 ./make.bash`
