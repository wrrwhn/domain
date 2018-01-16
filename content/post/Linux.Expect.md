+++
date = "2018-01-16T16:07:46+08:00"
title = ""
draft = false
tags = ["乐趣","饮食"]
share = true
+++

# Linux.Expect

## 参考
- [expect spawn、linux expect 用法](http://blog.csdn.net/ysdaniel/article/details/7059511)
- [expect spawn、linux expect 用法小记](https://www.centos.bz/2013/07/expect-spawn-linux-expect-usage/)


## 作用
- 代替实现与终端的交换，根据系统的输出运行相应的命令


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