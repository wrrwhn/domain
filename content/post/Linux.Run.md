+++
date = "2017-08-28T22:07:46+08:00"
title = "Linux.Run"
draft = false
tags = ["整理","Linux"]
share = true
+++


[TOC]

# 命令后台运行

## 参考
- [Linux 技巧：让进程在后台可靠运行的几种方法](https://www.ibm.com/developerworks/cn/linux/l-cn-nohup/)
- [linux命令后台运行](http://www.cnblogs.com/lwm-1988/archive/2011/08/20/2147299.html)
- [nohup命令](http://man.linuxde.net/nohup)
- [fg命令](http://man.linuxde.net/fg)


## 类型
- 后台运行，终端关闭时`停止`
- 后台运行，终端关掉后仍`继续运行`

## 方法
### 前后台切换
- 流程
    - 执行复杂语句
    - 使用 `Ctrl+ Z` 挂起复杂程序
        - `[1]+  Stopped                 /data/service/webapps/engine-dev/main`
    - 使用 `jobs` 查询后台工作情况
        - `[1]+  Stopped                 /data/service/webapps/engine-dev/main`
    - 输入 `fg 1` 继续执行复杂语句
    - 输入 `bg 1` 在后台继续执行语句
        - `[1]+ /data/service/webapps/engine-dev/main`
        - `jobs`
            - `[1]+  Running                 /data/service/webapps/engine-dev/main`

### 后台执行
- `&`
    - 后台运行，终端关闭时停止
        - 当前终端 shell 进程退出，会发送 hangup 信号给所有子进程
        - 子进程收到 hangup 后退出
    - 日志仍会打印到前台
    - 示例
        - `/data/service/webapps/engine-dev/main &`
        - `/data/service/webapps/engine-dev/main > out.file 2>&1 &`
            - `2>&1`
                - 将所有标准输出和错误输出重定向到 out.file
- `nohup`
    - 后台运行，终端关掉后
        - 不受当前 shell 退出影响
        - no hang up
