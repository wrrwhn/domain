---
title: "Linux.Make"
date: "2017-07-25"
categories:
 - "整理"
tags:
 - "Linux"
toc: true
---


# Make
## 参考
- [例解 Linux 下 Make 命令](http://www.cnblogs.com/hazir/p/linux_make_examples.html)

## 作用
- 编译和安装开源工具
- 管理大型复杂项目的编译

## 原理
- 配合 `Makefile` 文件进行文件与相应目标的对应操作
- 扫描 `Makefile` 找到目标及其依赖，并递归建立扫描、编译，后编译主目标
- 再次执行时，仅编译相关目标文件

## 参数
- `-B`
    - 让所有目标总是重新建立
- `-d`
    - 打印调试信息
- `-C`
    - 切换到指定目录执行
- `-f`
    - 指定 `Makefile` 配置文件名

## 示例
- 配置文件

    ```sh
    all: test
    test: test.o anotherTest.o
        gcc -Wall test.o anotherTest.o -o test
    test.o: test.c
        gcc -c -Wall test.c
    anotherTest.o: anotherTest.c
        gcc -c -Wall anotherTest.c
    clean:
        rm -rf *.o test
    ```
- `make`

    ```sh
    gcc -c -Wall test.c
    gcc -c -Wall anotherTest.c
    gcc -Wall test.o anotherTest.o -o test
    ```
- `make clean`

    ```sh
    rm -rf *.o test
    ```
