+++
date = "2017-11-15T22:07:46+08:00"
title = "Linux.Chown"
draft = false
tags = ["整理","Linux"]
share = true
+++


[TOC]

## chown
### 格式
- `chown [option]* [user[:[group]*]] [file|group]*`
### 必要参数
- `-c`
    + __显示更改__的部分的信息
- `-f`
    + __忽略错误__信息
- `-h`
    + 修复符号链接
- `-R`
    + 处理指定__目录以及其子目录下__的所有文件
- `-v`
    + __显示详细__的处理信息
- `-deference`
    + 作用于符号链接的指向，而不是链接文件本身
### 选择参数
- `--reference=<目录或文件>`
    + 把指定的目录/文件作为参考，把操作的文件/目录设置成参考文件/目录相同拥有者和群组
- `--from=<当前用户：当前群组>`
    + 只有当前用户和群组跟指定的用户和群组相同时才进行改变
- `--help`
    + 显示帮助信息
- `--version`
    + 显示版本信息
### 实例
- `chown -R appuser:appuser yao `
    + 将 root 权限下文件夹分配给 appuser 用户
