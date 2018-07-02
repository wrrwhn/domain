---
title: "Linux.Lsof"
date: "2017-12-11"
categories:
 - "整理"
tags:
 - "Linux"
toc: true
---


## 参考
- [lsof 一切皆文件](http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/lsof.html)

## 功能
- 查看当前系统文件的工具
- 支持文件类型
	- 普通文件
	- 目录
	- 网络文件系统的文件
	- 字符或设备文件
	- 函数共享库
	- 管道、命名管道
	- 符号链接
	- 网络文件（NFS、Socket）

## 参数
- `-a`
	-  列出打开文件存在的`进程`
- `-c <进程名>`
	-  列出指定`进程所打开的文件`
- `-g`
	-  列出GID号进程详情
- `-d <文件号>`
	-  列出`占用该文件号`的`进程`
- `+d <目录>`
	-  列出目录下被打开的文件
- `+D <目录>`
	-  递归列出目录下被打开的文件
- `-n <目录>`
	-  列出使用NFS的文件
- `-i <条件>` 
	-  列出符合条件的进程。（4、6、协议、:端口、 @ip）
- `-p <进程号>`
	-  列出指定`进程号所打开的文件`
- `-u`
	-  列出UID号进程详情
- `-h`
	-  显示帮助信息
- `-v`
	-  显示版本信息

## 示例
- `lsof | more`
	- Result
		| COMMAND  |    PID     | TID |    USER    |     FD     |   TYPE   |  DEVICE  | SIZE/OFF |   NODE   |                   NAME                   |
		|----------|------------|-----|------------|------------|----------|----------|----------|----------|------------------------------------------|
		| 进程名称 | 进程标识符 |     | 进程所有者 | 文件描述符 | 文件类型 | 指定磁盘 | 文件大小 | 索引节点 | 打开文件的确切名称                       |
		| systemd  | 1          |     | root       | cwd        | unknown  |          |          |          | /proc/1/cwd(readlink: Permission denied) |
	- FD
		- `cwd`
			- 表示`current work dirctory`，即应用程序的当前工作目录，这是该应用程序启动的目录，除非它本身对这个目录进行更改
		- `txt `
			- 该类型的文件是程序代码，如应用程序二进制文件本身或共享库，如上列表中显示的 /sbin/init 程序
		- `lnn`
			- library references (AIX);
		- `er`
			- FD information error (see NAME column);
		- `jld`
			- jail directory (FreeBSD);
		- `ltx`
			- shared library text (code and data);
		- `mxx `
			- hex memory-mapped type number xx.
		- `m86`
			- DOS Merge mapped file;
		- `mem`
			- memory-mapped file;
		- `mmap`
			- memory-mapped device;
		- `pd`
			- parent directory;
		- `rtd`
			- root directory;
		- `tr`
			- kernel trace file (OpenBSD);
		- `v86  VP/ix mapped file;
		- `0`
			- 表示标准输入
		- `1`
			- 表示标准输出
		- `2`
			- 表示标准错误一般在标准输出、标准错误、标准输入后还跟着文件状态模式`- r、w、u`等
		- `u`
			- 表示该文件被打开并处于`读取/写入模式`
		- `r`
			- 表示该文件被打开并处于`只读模式`
		- `w`
			- 表示该文件被打开并处于`只写模式`
		- `空格`
			- 表示该文件的状态模式为`unknow`，且`未被锁定`
		- `-`
			- 表示该文件的状态模式为`unknow`，且`被锁定`,同时在文件状态模式后面，还跟着相关的锁
		- `N`
			- for a Solaris NFS lock of unknown type;
		- `r`
			- for read lock on part of the file;
		- `R`
			- for a read lock on the entire file;
		- `w`
			- for a write lock on part of the file;（文件的部分写锁）
		- `W`
			- for a write lock on the entire file;（整个文件的写锁）
		- `u`
			- for a read and write lock of any length;
		- `U`
			- for a lock of unknown type;
		- `x`
			- for an SCO OpenServer Xenix lock on part      of the file;
		- `X`
			- for an SCO OpenServer Xenix lock on the      entire file;
		- `space`
			- if there is no lock.
	- TYPE
		- `DIR`
			- 表示目录
		- `CHR`
			- 表示字符类型
		- `BLK`
			- 块设备类型
		- `UNIX`
			-  UNIX 域套接字
		- `FIFO`
			- 先进先出 (FIFO) 队列
		- `IPv4`
			- 网际协议 (IP) 套接字
- `lsof -i :6666 -r 3`
	- `每三秒`查看`端口占用`情况
- `lsof error.log `
	- 查看指定`文件的相关进程`
- `lsof -u appuser | more`
	- 查看用户 appuser 打开的文件列表
- `lsof -c java | more`
	- 查看 Java `进程所打开`的文件
- `lsof -p 12617 | more`
	- 查看 12617 `进程所打开`的文件
- `lsof -n -i tcp | grep LISTEN | less`
	- 查看处于`连接状态的 TCP` 连接
