---
title: "Linux.Find"
date: "2018-08-09"
categories:
 - "整理"
tags:
 - "Linux"
toc: true
---

# Find
## 描述
- 用于指定目录下的文件查找

## 格式
- `find [-H] [-L] [-P] [-D debugopts] [-Olevel] [path...] [expression]`

## 参数
- `-P`
    - 表示不追随符号链接
- `-L`
    - 表示追随符号链接
- `-H`
    - 表示只追随命令行中的符号链接
- `-D <debug.option>`
    - debug的选项
- `-O <level>`
    - 开启查询的优化有1,2,3级

## 表达式
- 时间相关
    - `-[atime|mtime|ctime|amin|mmin|cmin] [+||-]n`
        - 指定时间内被存取、修改、更新权限的文件或目录，单位为 天、分钟
            - 其中数字前缀分别代表 `n单位时间之前`、`恰好n单位时间前`、`n单位时间之内被访问` 的情况
    - `-daystart`
        - 特指今天内进行访问、修改、更新权限的文件或目录
    - `-[anewer|cnewer|newer] <file>`
        - 查找存取、更新时间近于指定文件或目录时间的文件或目录
    - `-used \d`
        - 查找指定天数内查找文件或目录被更改之后在指定时间曾被存取过的文件或目录，单位以日计算

- 类型
    - `-typ <type>`
        - 过滤指定类型文件
        - 类型列表
            - `f`
                - 一般正规档案
            - `b, c`
                - 装置档案
            - `d`
                - 目录
            - `l`
                - 连结档
            - `s`
                - socket
            - `p`
                - FIFO
    - `-xtype <type>`
        - 针对符号连接进行检查
    - `-fstype <type>`
        - 只寻找该文件系统类型下的文件或目录

- 大小
    - `-size (+|-)<size>`
        - 查找符合指定的文件大小的文件，其中正负号指定范围外、内类型
        - 单位
            - `c`
                - 字节 byte
            - `w`
                - 字= 2c
            - `b`
                - 块= 512c
            - `k`
                - 1024 byte
            - `M`
                - 兆字节= 1024k
            - `G`
                - 吉字节= 1024M
    - `-expty`
        - 寻找0字节文件或空目录

- 范围
    - `-(xdev|mount)`
        - 仅在 **当前文件分区**下搜索
    - `-(user|group|uid|gid) <filter>`
        - 指定用户或群组名下的文件或目录
    - `-(nouser|nogroup)`
        - 指定不属于当前用户或群组的文件或目录
    - `-prune`
        - **跳过**指定目录

- 权限
    - `-perm <权限数值>`
        - 查找指定权限配置的文件或目录

- 正则
    - `-[regex|name|path] <regrex>`
        - 查找符合指定格式的字符串、文件名或路径的文件或目录
    - `-[iregex|iname|ipath] <regrex>`
        - 同上式，较之 **忽略大小写**

- 指定
    - `-inum<inode编号>`
        - 查找指定 `inode` 的文件或目录
    - `-links <连接数目>`
        - 查找指定`硬连接数目`的文件或目录

- 输出
    - `-print|print0|printf|ls`
        - `find`返回值为 `true` 时以多行、单行或指定格式、ls格式输出
        - `printf`为预设动作，且含有前缀 `./`
    - `-fprint|fprint0|fprintf|fls <file>`
        - 将上述数据保存至指定文件

- 执行
    - `-exec <command>`
        - `find` 执行结果为 `true` 时执行，格式如 `find path -exec command {} \;`
    - `-ok <command>`
        - 较 `-exec` 在执行指令前会先**询问用户**是否执行

- 限制
    - `-[maxdepth|mindepth] <level>`
        - 设置最大或最小目录层级
    - `-noleaf`
        - 不去考虑目录至少需拥有两个硬连接存在


- 其它
    - `-help或——help`
        - 在线帮助
    - `-version或——version`
        - 显示版本信息
    - `-depth`
        - 指定查询顺序，从最深层子目录开始
    - `-false|true`
        - 强制设置指令的返回值
    - `-follow`
        - 排除符号连接

## 示例

- 权限过滤

    ```sh
    ls -lh | grep "\-rw\-r\-\-r\-\-"
        -rw-r--r-- 1 appuser appuser     2 May  2 11:24 chattr-test.dat
        -rw-r--r-- 1 appuser appuser  1.5M Jan 18 12:08 from.gif

    find -perm 644
        ./from.gif
        ./chattr-test.dat
    ```

- 大小过滤

    ```sh
    # 过滤大于 50KB 的文件
    find -size +50k
    ```

- 文件名过滤

    ```sh
    # 寻找前缀为 `slide-rId3` 的文件
    find -name "slide-rId3*"
    ```

- 跳过

    ```sh
    # 搜索当下目录下的 txt 文件，但跳过 sk 目录
    find . -path "./sk" -prune -o -name "*.txt" -print
    ```

- 执行

    ```sh
    # 优化100K以上图片的分辨率
    find /data/cdn -regex '.*\(jpg\|JPG\|png\|PNG\|jpeg\|JPEG\)' -size +100k -exec convert resize "1024x768" -strip -quality 75% {} {} \;
    ```

# 对比
## locate
- 搜索自建数据库 `/var/lib/slocate/`，且仅`每天`建立一次预设
- 更新 `/etc/updatedb.conf` 中 **DAILY_UPDATE** 进行默认建立

## 结果
- **whereis= locate > find**


# 参考
## 官方
- [man.find](http://otzm88f21.bkt.clouddn.com/154e120c-1731-4427-8ff5-df3c085e5941.find)

## 补充
- [find命令](http://man.linuxde.net/find)
- [linux中find命令的详细用法](https://xionchen.github.io/2016/08/19/linux-find/)
- [Linux的五个查找命令](http://www.ruanyifeng.com/blog/2009/10/5_ways_to_search_for_files_using_the_terminal.html)
- [15个极好的Linux find命令示例 ](https://www.oschina.net/translate/15-practical-unix-linux-find-command-examples-part-2)