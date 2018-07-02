---
title: "Linux.Expect"
date: "2018-01-16"
categories:
 - "整理"
tags:
 - "Linux"
toc: true
---


# Linux.Expect

## 参考
- [expect spawn、linux expect 用法](http://blog.csdn.net/ysdaniel/article/details/7059511)
- [expect spawn、linux expect 用法小记](https://www.centos.bz/2013/07/expect-spawn-linux-expect-usage/)


## 作用
- 代替实现与终端的交换，根据系统的输出运行相应的命令
- 需要 Tcl 编程语言的支持


## 参数
### Run
- `-c`
    - 在命令行中直接执行
- `i`
    - 脚本把多个参数当成一个连续的列表
    - 事例
        - `expect -i arg1 arg2 arg3`
- `d`
    - 输出调试信息
- `D|Debug`
    - 用于是否立即启动调试器，后面接入参数 0/1
    - 事例
        - `expect -c 'set timeout 10' -D 1 -c 'set a 1'`
            - 优先执行 `set timeout 10`
            - 调适器启动后执行 `set a 1`
### SPAWN
- `-re`
    - 表示指定的字符串是一个正则表达式
- `eof`
    - 标识子进程已结束


## 安装
- `whereis tcl`
    - `yum install tcl`
- `whereis expect`
    - `yum install expect`


## 事例
- 自动登录
```
#!/usr/bin/expect 
set timeout 30                              # 设置延时 30 秒
spawn ssh -l username 192.168.1.1           # 进行 expect 模式，为后续运行进程加壳以传递交互指令
expect "password:"                          # 判断上一次输出结果是否包含 `password:`，有则继续执行后续指令
send "ispass\r"                             # 执行交换指令，输入 `ispass` 并回车
interact                                    # 完成后保持交互状态，把控件权交给控件台
```