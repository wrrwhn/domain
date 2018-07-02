---
title: "Go.GOPATH"
date: "2018-05-10"
categories:
 - "整理"
tags:
 - "Go"
toc: true
---


# 作用
- 于 `go/build` 中引入，罗列本机 Go 代码的位置，可用于解析导入语句
    - Unix
        - 使用 `:` 分割
    - **Windows**
        - 使用 `;` 分割
    - Plan 
        - 使用列表记录 
# 默认
- 未设置时，默认为用户主目录下的 `go` 文件夹
    - Unix
        - `$HOME/go`
    - Windows
        - `%USERPROFILE%\go`

# 补充
- 设置可参考 `Reference.SettingGOPATH`
- 可使用 `go env GOPATH` 查看当前配置
- 新 **包** 被 **下载** 时，将 **默认** 下载至 **列表目录中的首项** 目录地址中


# 目录结构

## 要求
- 配置的目录，需包含规定的结构

## 类型
- `src` 目录拥有 **源代码**，而 `src` 下目录决定了引用路径和可执行文件名
- `pkg` 目录拥有已安装的 **包对象**
    - 在 `Go` 的树型结构中，各目标 **操作系统**和体系 **架构**均有对应的 `pkg` 子目录
- `bin` 目录拥有已编译的 **命令**
    - `DIR/src/foo/quux` 将被安装至 `DIR/bin/quux`
    - 而当 `GOBIN` 被设置时，会 **指定生成**至其配置目录中
        - `GOBIN` 必须为一个 **绝对目录**
- `internal` 目录及其下级代码，仅可被 `internal` 的父目录下各目录代码引用
- `vendor`
    - `Go 1.6+` support
    - 使用外部依赖的 **本地拷贝**，以安全地引入
    - 仅可被 `vendor` 的父目录下各目录代码引用，同 `internal`，但引用时自动忽略 `/vendor` 前缀
        - /src/foo/vendor/baz/z.go
            - /src/foo/bar/x.go
                - import baz
            - /src/crash//bang/b.go
                - cannot import

## 示例
### basic
```
GOPATH=/home/user/go
/home/user/go/
    src/
        foo/
            bar/               (go code in package bar)
                x.go
            quux/              (go code in package main)
                y.go
                    // import foo/bar
    bin/
        quux                   (installed command)
    pkg/
        linux_amd64/
            foo/
                bar.a          (installed package object)
```


### internal
```
import foo/internal/baz/z.go
    noly work for foo/*
    
/home/user/go/
    src/
        crash/
            bang/
                b.go            // **NO**
        foo/                   
            f.go                // YES
            bar/               
                x.go            // YES
            internal/
                baz/           
                    z.go
            quux/              
                y.go            // YES
```


# Reference
- [Go.Help.GOPATH.log](http://otzm88f21.bkt.clouddn.com/411858e6-313f-4589-b9cb-a41a2718a5d9.log)
- [SettingGOPATH](https://golang.org/wiki/SettingGOPATH)
- [How to Write Go Code](https://golang.org/doc/code.html)
- [Go 1.4 “Internal” Packages](https://golang.org/s/go14internal)
- [Go 1.5 Vendor Experiment](https://golang.org/s/go15vendor)