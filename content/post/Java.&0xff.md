---
title: "Java.&0xff"
date: "2018-04-20"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---


# Code
- PipedInputStream.receive(int b)
	- `buffer[in++] = (byte)(b & 0xFF);`

# Reason
## 原因
- int= 		32bits
- byte= 	8bits
- 截取末 8 bit时，存在将数值位当符号位情况

## 示例
- int-> byte
	- 222 -> -34
		- 222.补码
			- 00000000 00000000 00000000 11011110
		- 截取
			- 11011110
		- 反码
			- 11011101
		- 原码
			- 10100010= -34
	- 解决方案
		- `222* 0xff`

# Supplement
## Java 使用 **补码** 进行数值存储
- 简化计算机运算，让符号位也参与运算，使计算机设计中只有加法，没有减法
- 示例
	- 1[00000001] + -1[11111111] = 0[1 00000000]= 0[00000000] 
	- 丢弃高位数值

## *码
- 原码
	- 符号位+ 数字二进制表示
	- +7 [00000111]
	- -7 [10000111]
- 反码
	- 正数时同原码，负数时，符号位不变，其它位数取反
	- +7 [00000111]
	- -7 [11111000]
- 补码
	- 正数时同原码，负数时则为反码+1
	- +7 [00000111]
	- -7 [11111001]


# Reference
- [java中byte转换int时为何与0xff进行与运算](http://www.blogjava.net/orangelizq/archive/2008/07/20/216228.html)
- [java原码、补码、反码总结](https://blog.csdn.net/qq_30739519/article/details/50991484)
- [原码, 反码, 补码 详解](http://www.cnblogs.com/zhangziqiu/archive/2011/03/30/ComputerCode.html)